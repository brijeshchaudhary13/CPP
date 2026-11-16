# Complete explanation of `enhance_bone_fast.cpp` — Function by Function

This program is a C++ implementation of an X-ray bone enhancement pipeline using Qt, OpenCV, wavelet denoising, noise estimation, Beer–Lambert attenuation, and multiscale image enhancement.

I'll explain it in execution order, starting with the basic helper functions and moving toward the complete X-ray enhancement algorithm.

The explanation will cover:

1. What each function does.
2. Why the function is needed.
3. How the algorithm works internally.
4. Important mathematical equations.
5. How each function connects to the next function.

# 1\. Overall architecture of the program

First, understand the complete processing pipeline.

## X-ray bone enhancement pipeline

Input RAW image (uint16)

Original X-ray pixel data

1\. Image analysis

- Air segmentation
- Body mask generation
- Noise model estimation
- Dark offset estimation

2\. Artifact removal

- Hot/dead pixel correction
- Stripe/grid removal
- Row/column noise correction

3\. Noise reduction

- Variance Stabilizing Transform (VST)
- Wavelet denoising
- Optional stronger denoising
- Non-Local Means smoothing

4\. X-ray enhancement

- Beer–Lambert attenuation
- Thickness compensation
- Gaussian-band enhancement
- Gamma correction
- CLAHE
- Sharpening

Output Enhanced\_Bone.raw

16-bit unsigned RAW image

Now let's understand every function.

# 2\. Global variables and logging functions

cpp

```cpp
static int PSTEP = 7;
static int NLM_TPL = 7, NLM_SRCH = 11;
static int NSHIFTS = 4;
static bool QUIET = false;
```

These variables control processing behavior.

| Variable | Purpose |
| --- | --- |
| `PSTEP` | Sampling interval for statistical calculations |
| `NLM_TPL` | Non-Local Means template window size |
| `NLM_SRCH` | Non-Local Means search window size |
| `NSHIFTS` | Number of cycle-spinning shifts |
| `QUIET` | Disables console logging |

### SHIFTS\_ALL

cpp

```cpp
static const int SHIFTS_ALL[4][2] = {
    {0, 0}, {3, 5}, {6, 2}, {1, 7}
};
```

These are spatial shifts used during wavelet denoising.

Why?

Wavelet thresholding can depend on pixel alignment. If an image is shifted before decomposition, the wavelet coefficients change.

The program processes multiple shifted versions and averages their reconstructed images.

This technique is called cycle spinning.

### LOG()

cpp

```cpp
static void LOG(const char *fmt, ...)
```

Prints messages to the console.

It uses variable arguments, similar to `printf()`.

Example:

cpp

```cpp
LOG("Image size: %d x %d", width, height);
```

### TIC()

cpp

```cpp
static void TIC(const char *fmt, ...)
```

Prints elapsed processing time.

It reports:

- Total time since program started.
- Time since the previous processing stage.

### DIE()

cpp

```cpp
static void DIE(const std::string &msg)
```

Prints an error message and terminates the program.

For example, if the RAW file cannot be opened, the program stops.

# 3\. Small helper functions

These functions provide reusable operations used throughout the algorithm.

## 3.1 parRows()

cpp

```cpp
template <class F>
static void parRows(int n, F f)
```

Purpose: Parallel processing of image rows.

Internally:

cpp

```cpp
cv::parallel_for_(cv::Range(0, n), ...);
```

Instead of processing all rows sequentially, OpenCV distributes the rows across available CPU threads.

For example:

cpp

```cpp
parRows(image.rows, [&](int y) {
    // Process row y
});
```

This improves processing speed for large X-ray images.

## 3.2 parN()

cpp

```cpp
template <class F>
static void parN(size_t N, F f)
```

Purpose: Parallel processing of a large one-dimensional array.

It divides the array into chunks of 65,536 elements.

For example:

cpp

```cpp
parN(image.total(), [&](size_t i0, size_t i1) {
    for (size_t i = i0; i < i1; ++i) {
        // Process pixels
    }
});
```

Difference:

- `parRows()` works row by row.
- `parN()` works on chunks of linear pixel indices.

## 3.3 fp()

cpp

```cpp
static inline const float *fp(const Mat &m)
static inline float *fp(Mat &m)
```

Returns a pointer to the floating-point pixel data.

Instead of repeatedly using:

cpp

```cpp
image.at<float>(y, x);
```

the program can access pixels directly through pointers.

This is faster for large images.

Important: These helpers assume that the matrix contains `CV_32F` data and is continuous when accessed as a single array.

## 3.4 smoothstepf()

cpp

```cpp
static inline float smoothstepf(float x)
```

Implements the smoothstep function:

$$
t=\operatorname{clamp}(x,0,1)
$$

$$
f(t)=t^2(3-2t)
$$

It smoothly transitions between 0 and 1.

For example:

| Input | Output |
| --- | --- |
| -0.5 | 0 |
| 0 | 0 |
| 0.25 | 0.156 |
| 0.5 | 0.5 |
| 0.75 | 0.844 |
| 1 | 1 |
| 1.5 | 1 |

It avoids sudden transitions when creating blending masks.

## 3.5 percentiles()

cpp

```cpp
static vector<double> percentiles(
    vector<float> v,
    const vector<double> &qs)
```

Calculates multiple percentiles from an array.

Example:

cpp

```cpp
percentiles(data, {1, 50, 99});
```

Returns:

- 1st percentile.
- 50th percentile (median).
- 99th percentile.

The function uses `nth_element()` to avoid sorting the complete array when only a few percentiles are needed.

This is important because the program handles millions of pixels.

## 3.6 percentile()

cpp

```cpp
static double percentile(
    const vector<float> &v, double q)
```

Calculates one percentile.

It internally calls `percentiles()`.

## 3.7 medianOf()

cpp

```cpp
static double medianOf(const vector<float> &v)
```

Calculates the 50th percentile.

The median is useful for robust estimation because it is less affected by extreme pixel values.

## 3.8 meanOf()

cpp

```cpp
static double meanOf(const vector<float> &v)
```

Calculates:

$$
\mu=\frac{1}{N}\sum_{i=1}^{N}x_i
$$

Used for image intensity statistics.

## 3.9 stdOf()

cpp

```cpp
static double stdOf(const vector<float> &v)
```

Calculates standard deviation:

$$
\sigma=
\sqrt{
\frac{1}{N}
\sum_{i=1}^{N}(x_i-\mu)^2
}
$$

Used in noise estimation and signal-to-noise calculations.

## 3.10 stridedAll()

cpp

```cpp
static vector<float> stridedAll(
    const Mat &m, int step)
```

Samples image pixels at regular intervals.

For example:

cpp

```cpp
stridedAll(image, 7);
```

Selects every seventh pixel.

This reduces computational cost when calculating approximate image statistics.

## 3.11 gather()

cpp

```cpp
static vector<float> gather(
    const Mat &m,
    const vector<int> &idx)
```

Extracts pixels at specific indices.

For example, if:

cpp

```cpp
idx = {0, 10, 20, 30};
```

It extracts those four pixel values.

## 3.12 gatherMask()

cpp

```cpp
static vector<float> gatherMask(
    const Mat &m, const Mat &mask)
```

Extracts only pixels where the mask is nonzero.

This is particularly important because the program often needs statistics from the body region while excluding air.

## 3.13 sampleIdx()

cpp

```cpp
static vector<int> sampleIdx(const Mat &mask)
```

Creates a list of pixel indices from a mask.

It uses `PSTEP` to reduce the number of samples.

## 3.14 anyNZ()

cpp

```cpp
static bool anyNZ(const Mat &m)
```

Checks whether the matrix contains any nonzero pixels.

## 3.15 interp1()

cpp

```cpp
static double interp1(
    double x,
    const vector<double> &xp,
    const vector<double> &fpv)
```

Performs one-dimensional linear interpolation.

Suppose:

$$
x_p=[10,20]
$$

$$
f_p=[100,200]
$$

For $x=15$:

$$
f(15)=150
$$

This function is used in noise models, VST reconstruction, and histogram equalization.

## 3.16 roll2()

cpp

```cpp
static Mat roll2(
    const Mat &m, int dy, int dx)
```

Circularly shifts an image.

For example:

- `dy = 3`: shift vertically by three pixels.
- `dx = 5`: shift horizontally by five pixels.

Pixels leaving one edge reappear on the opposite edge.

It is used in wavelet cycle spinning.

# 4\. Morphological operations

These functions work with binary masks.

## 4.1 crossK()

cpp

```cpp
static const Mat &crossK()
```

Creates a 3 × 3 cross-shaped structuring element.

Cross-shaped kernel

0

1

0

1

1

1

0

1

0

Blue cells represent active kernel positions.

## 4.2 er()

cpp

```cpp
static Mat er(const Mat &m, int n)
```

Performs binary erosion.

Erosion shrinks foreground regions.

Uses:

cpp

```cpp
cv::erode()
```

In this program, erosion is used to remove unreliable pixels near the air/body boundary.

## 4.3 di()

cpp

```cpp
static Mat di(const Mat &m, int n)
```

Performs binary dilation.

Dilation expands foreground regions.

Uses:

cpp

```cpp
cv::dilate()
```

It is used to fill small gaps and expand segmented air regions.

## 4.4 taxiDist()

cpp

```cpp
static Mat taxiDist(const Mat &mask)
```

Calculates the distance from each foreground pixel to the nearest background pixel using an L1 distance transform.

$$
D(x,y)=\min_{(u,v)\in Background}
(|x-u|+|y-v|)
$$

This is used to determine how far pixels are from the body boundary.

## 4.5 Filler structure

cpp

```cpp
struct Filler
```

This structure stores information needed to fill air pixels using nearby foreground pixels.

It contains:

cpp

```cpp
bool identity;
vector<int> idx;
```

### Filler::apply()

cpp

```cpp
Mat apply(const Mat &img) const
```

Creates an image where air pixels are replaced with nearby body pixel values.

Why is this needed?

FFT, Gaussian filtering, and wavelet operations can produce artificial edges if large air regions contain very different values from the body.

Filling air regions reduces these boundary artifacts.

### buildFiller()

cpp

```cpp
static Filler buildFiller(const Mat &fg)
```

Builds the nearest-foreground mapping.

It uses:

cpp

```cpp
cv::distanceTransform()
```

to identify nearby foreground pixels.

The resulting index map is reused instead of calculating nearest foreground pixels every time.

## 4.6 logOf()

cpp

```cpp
static Mat logOf(
    const Mat &z, double d, double c)
```

Applies logarithmic transformation:

$$
L(x,y)=\ln(\max(z(x,y)-d+c,1))
$$

Where:

- $z$: original or corrected X-ray intensity.
- $d$: dark offset.
- $c$: stabilization constant.

Why logarithmic transformation?

X-ray intensity approximately follows Beer–Lambert attenuation:

$$
I=I_0e^{-\mu t}
$$

Taking logarithms:

$$
-\ln\left(\frac{I}{I_0}\right)=\mu t
$$

This converts exponential intensity changes into approximately linear attenuation values.

It is fundamental to the enhancement pipeline.

# 5\. Wavelet transform functions

This section is important because it implements wavelet denoising without using the Python PyWavelets library.

The code implements its own 2D discrete wavelet transform.

## 5.1 Wav structure

cpp

```cpp
struct Wav {
    int L;
    float h[8], g[8];
};
```

Stores wavelet filter coefficients.

- `h`: low-pass reconstruction filter.
- `g`: high-pass reconstruction filter.
- `L`: filter length.

## 5.2 makeWav()

cpp

```cpp
static Wav makeWav(
    const double *rec_lo, int L)
```

Builds the wavelet filters.

The high-pass filter is generated from the low-pass coefficients using alternating signs and reversed ordering.

$$
g[n]=(-1)^n h[L-1-n]
$$

## 5.3 sym4()

cpp

```cpp
static const Wav &sym4()
```

Defines the Symlet-4 wavelet.

This is the main wavelet used for denoising.

Symlet wavelets provide good localization of image details.

## 5.4 db2()

cpp

```cpp
static const Wav &db2()
```

Defines the Daubechies-2 wavelet.

It is used for noise estimation through diagonal wavelet coefficients.

## 5.5 anaV()

cpp

```cpp
static void anaV(
    const Mat &in,
    Mat &lo,
    Mat &hi,
    const Wav &w)
```

Performs vertical wavelet decomposition.

It generates:

- `lo`: vertical low-frequency component.
- `hi`: vertical high-frequency component.

The image height is reduced by half.

Mathematically:

$$
L[k,x]=\sum_n h[n]I[2k+n,x]
$$

$$
H[k,x]=\sum_n g[n]I[2k+n,x]
$$

## 5.6 anaH()

cpp

```cpp
static void anaH(
    const Mat &in,
    Mat &lo,
    Mat &hi,
    const Wav &w)
```

Performs horizontal wavelet decomposition.

Combined with `anaV()`, it produces four subbands.

2D wavelet decomposition

LL

Approximation

LH

Directional details

HL

Directional details

HH

Diagonal details

Conceptual four-band decomposition. The actual array naming follows the code's filtering order.

- LL contains low-frequency image information.
- LH and HL contain directional details.
- HH contains diagonal high-frequency details.

## 5.7 synH()

cpp

```cpp
static Mat synH(
    const Mat &lo,
    const Mat &hi,
    const Wav &w)
```

Reconstructs the horizontal dimension.

It combines low-pass and high-pass coefficients.

## 5.8 synV()

cpp

```cpp
static Mat synV(
    const Mat &lo,
    const Mat &hi,
    const Wav &w)
```

Reconstructs the vertical dimension.

Together, `synH()` and `synV()` reconstruct the original image resolution.

## 5.9 dwt2()

cpp

```cpp
static void dwt2(
    const Mat &in,
    Mat &A,
    Mat &B,
    Mat &C,
    Mat &D,
    const Wav &w)
```

Performs complete 2D wavelet decomposition.

It calls:

1. `anaV()`
2. `anaH()` twice.

Outputs:

| Output | Meaning |
| --- | --- |
| A | LL approximation |
| B | Horizontal detail branch |
| C | Vertical detail branch |
| D | Diagonal detail |

## 5.10 idwt2()

cpp

```cpp
static Mat idwt2(
    const Mat &A,
    const Mat &B,
    const Mat &C,
    const Mat &D,
    const Wav &w)
```

Performs inverse 2D wavelet reconstruction.

It calls:

1. `synH()`
2. `synV()`

The four subbands are combined into a reconstructed image.

## 5.11 softThr()

cpp

```cpp
static Mat softThr(
    const Mat &b, double t)
```

Implements soft thresholding.

Equation:

$$
\hat{x}=
\begin{cases}
x-t & x>t\\
x+t & x<-t\\
0 & |x|\leq t
\end{cases}
$$

Purpose: Suppress small wavelet coefficients that are likely to represent noise.

Large coefficients are retained because they are more likely to contain useful structures.

# 6\. Wavelet denoising

## waveletMulti()

cpp

```cpp
static vector<Mat> waveletMulti(
    const Mat &y,
    const vector<double> &mults,
    double sigma = 1.0,
    int levels = 4)
```

This is one of the most important functions.

It performs:

- Symlet-4 wavelet decomposition.
- Four-level decomposition.
- Noise threshold calculation.
- Soft thresholding.
- Inverse wavelet reconstruction.
- Cycle spinning.
- Averaging of shifted reconstructions.

### Step 1: Padding

cpp

```cpp
int pad = 1 << levels;
```

For four levels:

$$
pad=2^4=16
$$

The image is padded so its dimensions are divisible by 16.

### Step 2: Cycle spinning

cpp

```cpp
for (int si = 0; si < NSHIFTS; ++si)
```

The image is shifted before wavelet decomposition.

This reduces shift-dependent wavelet artifacts.

### Step 3: Wavelet decomposition

cpp

```cpp
dwt2(cur, A, B, C, D, W);
```

The image is decomposed into four frequency bands.

The LL band is decomposed again at the next level.

### Step 4: Noise threshold estimation

cpp

```cpp
double sx = sqrt(max(s2 / n - sigma * sigma, 0.0));
base[l][j] = sx > 0
    ? sigma * sigma / sx
    : mx;
```

This estimates the signal-dependent threshold using a BayesShrink-style approach.

The threshold is:

$$
T=\frac{\sigma_n^2}{\sigma_x}
$$

Where:

- $\sigma_n$: noise standard deviation.
- $\sigma_x$: estimated signal standard deviation.

### Step 5: Soft thresholding

cpp

```cpp
Mat t0 = softThr(lv[l][0], base[l][0] * mults[mi]);
```

The threshold is multiplied by a user-defined multiplier.

Higher multipliers produce stronger denoising.

### Step 6: Reconstruction

cpp

```cpp
rec = idwt2(rec, t0, t1, t2, W);
```

Reconstructs the image from the thresholded wavelet coefficients.

### Step 7: Reverse shift

cpp

```cpp
Mat back = roll2(
    rec(cv::Rect(0, 0, w, h)),
    -dy, -dx);
```

Restores the original alignment.

### Step 8: Averaging

cpp

```cpp
acc[mi] *= 1.0f / NSHIFTS;
```

Averages the shifted reconstructions.

Result: A denoised image with reduced shift-dependent artifacts.

# 7\. Additional denoising functions

## 7.1 nlmY()

cpp

```cpp
static Mat nlmY(
    const Mat &y,
    double h_sigma,
    double ylo,
    double yhi)
```

Performs Non-Local Means denoising.

It first converts the floating-point image to 16-bit unsigned format.

Then calls:

cpp

```cpp
cv::fastNlMeansDenoising()
```

Non-Local Means works by comparing image patches.

Instead of only considering neighboring pixels, it searches for similar patches in a larger search region.

This helps preserve repeating structures while reducing noise.

Parameters:

- `h_sigma`: denoising strength.
- `NLM_TPL`: template window size.
- `NLM_SRCH`: search window size.

## 7.2 bilateralY()


```cpp
// enhance_bone_fast.cpp - C++/Qt/OpenCV port of enhance_bone_fast.py (xray level, RAW output only)
//
// GRAIN-REDUCTION FIXES (search for "FIX" in this file):
//   FIX 1  noise model uses the 50th percentile of block sigmas (was 25th -> sigma underestimated)
//   FIX 2  denoise gate raised from detail-SNR < 30 to < 60
//   FIX 3  standard profile: finest band gains reduced, bone_gain 1.4 -> 1.0
//   FIX 4  standard profile: sharpen, coring threshold 0.012 -> 0.03
//   FIX 5  standard profile: gamma, CLAHE strength, clip reduced
//   FIX 6  stronger default denoise (--denoise-scale 1.5) and NLM smoothing (--smooth 1.0)
//
// SPEED OPTIMISATIONS (search for "OPT" in this file):
//   OPT 1  wavelet: polyphase (contiguous, vectorisable) analysis/synthesis, 2 cycle-spin shifts unless --speed exact,
//          parallel soft-threshold, cv::norm for coefficient statistics
//   OPT 2  NLM: template 5 / search 9 in "fast" mode, parallel quantise/dequantise
//   OPT 3  stripe detection on a Welch-averaged 512x512 tile spectrum instead of one full-size FFT
//   OPT 4  filler + final distance transform run concurrently with other stages; cheaper distance masks
//   OPT 5  sampled medians (PSTEP) in segmentAir / detailSNR / safety check
//   OPT 6  line-noise column pass uses a transposed copy (cache friendly)
//   OPT 7  (probe now half-res, see BRT 4) parallel sharpen, fast tanh, float log, gaussBig in detailSNR,
//          parallel output packing
//
// BRIGHTNESS FIXES (search for "BRT" in this file):
//   BRT 1  output brightness log (p1/p25/median/p75/p99, near-white %) for every run
//   BRT 2  auto gamma: standard profile only darkens images whose body median would exceed --target-median
//          (gamma = clamp(log(target)/log(median), default, 2.6); never brighter than the fixed default)
//   BRT 3  soft shoulder after sharpening so overshoot does not clip bone edges to pure white
//   BRT 4  view probe back at half resolution (quarter-res could flip an image into the bright THICK profile)
//          -> --view standard|thick|auto still overrides
//
// BUILD:  Release, -O3 -march=native   (do NOT use -ffast-math: NAN / INFINITY are used)
//         MSVC: /O2 /arch:AVX2          OpenCV should be built with TBB or OpenMP.
//
// Pipeline: air mask -> hot-pixel repair -> stripe/grid removal -> line-noise removal -> VST wavelet denoise (+NLM)
//           -> Beer-Lambert attenuation -> thickness compensation -> soft-limited Gaussian-band gains
//           -> window/gamma -> 16-bit CLAHE -> RAW uint16
//
// Usage:  enhance_bone_fast Original.raw --width 3072 --height 3072 --outdir out [--speed exact|fast|turbo]
//
// Notes:
//  * scipy.fft  -> cv::dft          * pywt (sym4/db2, periodization) -> own orthogonal DWT below
//  * scipy.ndimage -> OpenCV morphology / connectedComponents
//  * the "--denoiser auto" benchmark is NOT ported (falls back to wavelet).

#include <QCoreApplication>
#include <QCommandLineParser>
#include <QDir>
#include <QFile>
#include <QFileInfo>
#include <QThread>
#include <QElapsedTimer>
#include <QMutex>
#include <QMutexLocker>
#include <QtConcurrent>

#include <opencv2/core.hpp>
#include <opencv2/imgproc.hpp>
#include <opencv2/photo.hpp>

#include <algorithm>
#include <cmath>
#include <cstdarg>
#include <cstdio>
#include <cstdint>
#include <cstring>
#include <string>
#include <vector>

using cv::Mat;
using std::vector;

#if defined(_MSC_VER)
#define RESTRICT __restrict
#else
#define RESTRICT __restrict__
#endif

// ======================================================================================
// globals / logging
// ======================================================================================
static int  PSTEP = 7;
static int  NLM_TPL = 7, NLM_SRCH = 11;
static int  NSHIFTS = 4;
static bool QUIET = false;
static const int SHIFTS_ALL[4][2] = {{0, 0}, {3, 5}, {6, 2}, {1, 7}};
static QElapsedTimer g_t0;
static double g_last = 0.0;

static void LOG(const char *fmt, ...)
{
    if (QUIET) return;
    char buf[2048];
    va_list ap; va_start(ap, fmt); vsnprintf(buf, sizeof buf, fmt, ap); va_end(ap);
    fputs(buf, stdout); fputc('\n', stdout); fflush(stdout);
}
static void TIC(const char *fmt, ...)
{
    char buf[1024];
    va_list ap; va_start(ap, fmt); vsnprintf(buf, sizeof buf, fmt, ap); va_end(ap);
    double now = g_t0.nsecsElapsed() * 1e-9;
    LOG("[%6.2fs | +%5.2fs] %s", now, now - g_last, buf);
    g_last = now;
}
static void DIE(const std::string &msg) { fprintf(stderr, "ERROR: %s\n", msg.c_str()); exit(1); }

// ======================================================================================
// small helpers
// ======================================================================================
template <class F> static void parRows(int n, F f)
{
    cv::parallel_for_(cv::Range(0, n), [&](const cv::Range &r) { for (int y = r.start; y < r.end; ++y) f(y); });
}
template <class F> static void parN(size_t N, F f)   // f(i0, i1) on chunks
{
    const size_t B = 65536;
    cv::parallel_for_(cv::Range(0, (int)((N + B - 1) / B)), [&](const cv::Range &r) {
        for (int b = r.start; b < r.end; ++b) { size_t i0 = (size_t)b * B; f(i0, std::min(N, i0 + B)); }
    });
}
static inline const float *fp(const Mat &m) { return reinterpret_cast<const float *>(m.data); }
static inline float *fp(Mat &m) { return reinterpret_cast<float *>(m.data); }

static inline float smoothstepf(float x) { x = std::min(std::max(x, 0.f), 1.f); return x * x * (3.f - 2.f * x); }

// OPT 7: Pade [7/6] approximation of tanh (vectorisable, abs error ~1e-5)
static inline float fastTanh(float x)
{
    x = std::min(std::max(x, -4.97f), 4.97f);
    float x2 = x * x;
    return x * (135135.f + x2 * (17325.f + x2 * (378.f + x2))) /
           (135135.f + x2 * (62370.f + x2 * (3150.f + x2 * 28.f)));
}

static Mat gaussBig(const Mat &x, double sigma);   // defined further below (used by detailSNR)

static vector<double> percentiles(vector<float> v, const vector<double> &qs)
{
    vector<double> out(qs.size(), 0.0);
    size_t n = v.size();
    if (!n) return out;
    if (qs.size() > 16) {
        std::sort(v.begin(), v.end());
        for (size_t k = 0; k < qs.size(); ++k) {
            double pos = qs[k] / 100.0 * (n - 1);
            size_t i = (size_t)std::floor(pos);
            double fr = pos - (double)i, lo = v[i], hi = (i + 1 < n) ? v[i + 1] : lo;
            out[k] = lo + (hi - lo) * fr;
        }
        return out;
    }
    for (size_t k = 0; k < qs.size(); ++k) {
        double pos = qs[k] / 100.0 * (n - 1);
        size_t i = (size_t)std::floor(pos);
        double fr = pos - (double)i;
        std::nth_element(v.begin(), v.begin() + i, v.end());
        double lo = v[i], hi = lo;
        if (fr > 0 && i + 1 < n) hi = *std::min_element(v.begin() + i + 1, v.end());
        out[k] = lo + (hi - lo) * fr;
    }
    return out;
}
static double percentile(const vector<float> &v, double q) { return percentiles(v, vector<double>(1, q))[0]; }
static double medianOf(const vector<float> &v) { return percentile(v, 50.0); }

static double meanOf(const vector<float> &v)
{
    if (v.empty()) return 0.0;
    double s = 0; for (float x : v) s += x; return s / v.size();
}
static double stdOf(const vector<float> &v)
{
    if (v.empty()) return 0.0;
    double m = meanOf(v), s = 0; for (float x : v) s += (x - m) * (x - m); return std::sqrt(s / v.size());
}

static vector<float> stridedAll(const Mat &m, int step)
{
    vector<float> v; size_t n = m.total(); v.reserve(n / step + 1);
    const float *p = fp(m);
    for (size_t i = 0; i < n; i += step) v.push_back(p[i]);
    return v;
}
static vector<float> gather(const Mat &m, const vector<int> &idx)
{
    vector<float> v(idx.size()); const float *p = fp(m);
    for (size_t i = 0; i < idx.size(); ++i) v[i] = p[idx[i]];
    return v;
}
static vector<float> gatherMask(const Mat &m, const Mat &mask)
{
    vector<float> v; const float *p = fp(m); const uchar *q = mask.data; size_t n = m.total();
    for (size_t i = 0; i < n; ++i) if (q[i]) v.push_back(p[i]);
    return v;
}
// OPT 5: sampled version (every PSTEP-th masked pixel); identical to gatherMask when PSTEP == 1 (exact mode)
static vector<float> gatherMaskS(const Mat &m, const Mat &mask)
{
    vector<float> v; const float *p = fp(m); const uchar *q = mask.data; size_t n = m.total(), c = 0;
    const size_t step = (size_t)std::max(PSTEP, 1);
    for (size_t i = 0; i < n; ++i) if (q[i]) { if (c % step == 0) v.push_back(p[i]); ++c; }
    return v;
}
static vector<int> sampleIdx(const Mat &mask)
{
    vector<int> v; const uchar *p = mask.data; size_t n = mask.total(), cnt = 0;
    for (size_t i = 0; i < n; ++i) if (p[i]) { if (cnt % PSTEP == 0) v.push_back((int)i); ++cnt; }
    return v;
}
static bool anyNZ(const Mat &m) { return cv::countNonZero(m) > 0; }

static double interp1(double x, const vector<double> &xp, const vector<double> &fpv)
{
    if (x <= xp.front()) return fpv.front();
    if (x >= xp.back()) return fpv.back();
    size_t i = std::upper_bound(xp.begin(), xp.end(), x) - xp.begin();   // xp[i-1] <= x < xp[i]
    double dx = xp[i] - xp[i - 1];
    if (dx <= 0) return fpv[i];
    return fpv[i - 1] + (fpv[i] - fpv[i - 1]) * (x - xp[i - 1]) / dx;
}

static Mat roll2(const Mat &m, int dy, int dx)
{
    int h = m.rows, w = m.cols;
    dy = ((dy % h) + h) % h; dx = ((dx % w) + w) % w;
    Mat o(h, w, CV_32F);
    for (int y = 0; y < h; ++y) {
        const float *s = m.ptr<float>(y);
        float *d = o.ptr<float>((y + dy) % h);
        if (dx == 0) std::memcpy(d, s, sizeof(float) * w);
        else {
            std::memcpy(d + dx, s, sizeof(float) * (w - dx));
            std::memcpy(d, s + (w - dx), sizeof(float) * dx);
        }
    }
    return o;
}

// ---- morphology (cross element, border value 0 like scipy)
static const Mat &crossK() { static Mat k = cv::getStructuringElement(cv::MORPH_CROSS, cv::Size(3, 3)); return k; }
static Mat er(const Mat &m, int n) { Mat o; cv::erode(m, o, crossK(), cv::Point(-1, -1), n, cv::BORDER_CONSTANT, cv::Scalar(0)); return o; }
static Mat di(const Mat &m, int n) { Mat o; cv::dilate(m, o, crossK(), cv::Point(-1, -1), n, cv::BORDER_CONSTANT, cv::Scalar(0)); return o; }

static Mat taxiDist(const Mat &mask)
{
    Mat p, d;
    cv::copyMakeBorder(mask, p, 1, 1, 1, 1, cv::BORDER_CONSTANT, cv::Scalar(0));
    cv::distanceTransform(p, d, cv::DIST_L1, 3);
    return d(cv::Rect(1, 1, mask.cols, mask.rows)).clone();
}

// ---- nearest-foreground fill (computed once)
struct Filler {
    bool identity = false;
    vector<int> idx;
    Mat apply(const Mat &img) const
    {
        if (identity) return img.clone();
        Mat o(img.size(), CV_32F);
        const float *s = fp(img); float *d = fp(o); size_t n = img.total();
        parN(n, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) d[i] = s[idx[i]]; });
        return o;
    }
};
static Filler buildFiller(const Mat &fg)
{
    Filler f;
    int nfg = cv::countNonZero(fg);
    if (nfg == (int)fg.total() || nfg == 0) { f.identity = true; return f; }
    Mat air8, dist, lab;
    cv::compare(fg, 0, air8, cv::CMP_EQ);                       // non-zero = air
    // OPT 4: 3x3 mask is enough for "nearest body pixel"
    cv::distanceTransform(air8, dist, lab, cv::DIST_L2, 3, cv::DIST_LABEL_PIXEL);
    size_t n = fg.total();
    vector<int> map(n + 2, -1);
    const int *pl = lab.ptr<int>(); const uchar *pf = fg.data;
    for (size_t i = 0; i < n; ++i) if (pf[i]) map[pl[i]] = (int)i;     // each body pixel carries its own label
    f.idx.resize(n);
    for (size_t i = 0; i < n; ++i) { int m = (pl[i] >= 0 && (size_t)pl[i] < map.size()) ? map[pl[i]] : -1; f.idx[i] = (m >= 0) ? m : (int)i; }
    return f;
}

// OPT 7: float arithmetic + logf
static Mat logOf(const Mat &z, double d, double c)
{
    Mat o(z.size(), CV_32F);
    const float df = (float)d, cf = (float)c;
    parRows(z.rows, [&](int y) {
        const float *s = z.ptr<float>(y); float *p = o.ptr<float>(y);
        for (int x = 0; x < z.cols; ++x) p[x] = std::log(std::max(s[x] - df + cf, 1.0f));
    });
    return o;
}

// ======================================================================================
// Orthogonal periodised DWT (sym4 / db2)   -- replaces pywt
//   analysis:  a[k] = sum_n h[n] x[2k+n],  d[k] = sum_n g[n] x[2k+n]   (indices mod N)
//   synthesis: x[m] = sum_k a[k] h[m-2k] + d[k] g[m-2k]
// ======================================================================================
struct Wav { int L; float h[8], g[8]; };
static Wav makeWav(const double *rec_lo, int L)
{
    Wav w; w.L = L;
    for (int n = 0; n < L; ++n) w.h[n] = (float)rec_lo[n];
    for (int n = 0; n < L; ++n) w.g[n] = ((n & 1) ? -1.f : 1.f) * (float)rec_lo[L - 1 - n];
    return w;
}
static const Wav &sym4()
{
    static const double c[8] = {0.0322231006040427, -0.012603967262037833, -0.09921954357684722, 0.29785779560527736,
                                0.8037387518059161, 0.49761866763201545, -0.02963552764599851, -0.07576571478927333};
    static Wav w = makeWav(c, 8); return w;
}
static const Wav &db2()
{
    static const double c[4] = {0.48296291314469025, 0.836516303737469, 0.22414386804185735, -0.12940952255092145};
    static Wav w = makeWav(c, 4); return w;
}

static void anaV(const Mat &in, Mat &lo, Mat &hi, const Wav &w)
{
    int H = in.rows, W = in.cols, L = w.L;
    lo.create(H / 2, W, CV_32F); hi.create(H / 2, W, CV_32F);
    parRows(H / 2, [&](int k) {
        float *pl = lo.ptr<float>(k), *ph = hi.ptr<float>(k);
        std::fill(pl, pl + W, 0.f); std::fill(ph, ph + W, 0.f);
        for (int n = 0; n < L; ++n) {
            const float *s = in.ptr<float>((2 * k + n) % H); float hn = w.h[n], gn = w.g[n];
            for (int x = 0; x < W; ++x) { pl[x] += hn * s[x]; ph[x] += gn * s[x]; }
        }
    });
}

// OPT 1: polyphase version -> every inner loop is contiguous and vectorisable
static void anaH(const Mat &in, Mat &lo, Mat &hi, const Wav &w)
{
    const int R = in.rows, W = in.cols, W2 = W / 2, L = w.L, P = L / 2;
    lo.create(R, W2, CV_32F); hi.create(R, W2, CV_32F);
    parRows(R, [&](int r) {
        vector<float> eo(2 * (W2 + P));
        float *E = eo.data(), *O = E + (W2 + P);
        const float *s = in.ptr<float>(r);
        for (int k = 0; k < W2; ++k) { E[k] = s[2 * k]; O[k] = s[2 * k + 1]; }
        for (int j = 0; j < P; ++j) { E[W2 + j] = E[j]; O[W2 + j] = O[j]; }   // periodic wrap
        float *RESTRICT pl = lo.ptr<float>(r), *RESTRICT ph = hi.ptr<float>(r);
        for (int k = 0; k < W2; ++k) { pl[k] = 0.f; ph[k] = 0.f; }
        for (int n = 0; n < L; ++n) {
            const float *RESTRICT src = ((n & 1) ? O : E) + (n >> 1);
            const float hn = w.h[n], gn = w.g[n];
            for (int k = 0; k < W2; ++k) { pl[k] += hn * src[k]; ph[k] += gn * src[k]; }
        }
    });
}

static Mat synH(const Mat &lo, const Mat &hi, const Wav &w)
{
    const int R = lo.rows, W2 = lo.cols, W = 2 * W2, L = w.L, P = L / 2;
    Mat out(R, W, CV_32F);
    parRows(R, [&](int r) {
        vector<float> buf(2 * (W2 + P) + 2 * W2, 0.f);
        float *Ae = buf.data(), *De = Ae + (W2 + P), *oe = De + (W2 + P), *oo = oe + W2;
        const float *pl = lo.ptr<float>(r), *ph = hi.ptr<float>(r);
        for (int j = 0; j < P; ++j) { Ae[j] = pl[W2 - P + j]; De[j] = ph[W2 - P + j]; }   // negative indices wrap
        for (int k = 0; k < W2; ++k) { Ae[P + k] = pl[k]; De[P + k] = ph[k]; }
        for (int p = 0; p < P; ++p) {
            const float *RESTRICT a = Ae + P - p, *RESTRICT d = De + P - p;   // a[j] == A[j-p]
            const float he = w.h[2 * p], ho = w.h[2 * p + 1], ge = w.g[2 * p], go = w.g[2 * p + 1];
            for (int j = 0; j < W2; ++j) { oe[j] += he * a[j] + ge * d[j]; oo[j] += ho * a[j] + go * d[j]; }
        }
        float *po = out.ptr<float>(r);
        for (int j = 0; j < W2; ++j) { po[2 * j] = oe[j]; po[2 * j + 1] = oo[j]; }
    });
    return out;
}
static Mat synV(const Mat &lo, const Mat &hi, const Wav &w)
{
    int H2 = lo.rows, W = lo.cols, H = 2 * H2, L = w.L;
    Mat out(H, W, CV_32F);
    parRows(H, [&](int m) {
        float *po = out.ptr<float>(m); std::fill(po, po + W, 0.f);
        for (int n = 0; n < L; ++n) {
            if (((m - n) & 1) != 0) continue;
            int k = ((m - n + H) % H) / 2;
            const float *a = lo.ptr<float>(k), *d = hi.ptr<float>(k); float hn = w.h[n], gn = w.g[n];
            for (int x = 0; x < W; ++x) po[x] += hn * a[x] + gn * d[x];
        }
    });
    return out;
}
static void dwt2(const Mat &in, Mat &A, Mat &B, Mat &C, Mat &D, const Wav &w)
{
    Mat vl, vh; anaV(in, vl, vh, w); anaH(vl, A, B, w); anaH(vh, C, D, w);
}
static Mat idwt2(const Mat &A, const Mat &B, const Mat &C, const Mat &D, const Wav &w)
{
    Mat vl = synH(A, B, w), vh = synH(C, D, w); return synV(vl, vh, w);
}

// OPT 1: parallel soft threshold
static Mat softThr(const Mat &b, double t)
{
    Mat o(b.size(), CV_32F); const float *s = fp(b); float *d = fp(o); const float tf = (float)t;
    parN(b.total(), [&](size_t i0, size_t i1) {
        for (size_t i = i0; i < i1; ++i) { float v = s[i]; d[i] = std::copysign(std::max(std::fabs(v) - tf, 0.f), v); }
    });
    return o;
}

// BayesShrink soft-threshold wavelet denoise with cycle spinning; one decomposition serves all multipliers.
static vector<Mat> waveletMulti(const Mat &y, const vector<double> &mults, double sigma = 1.0, int levels = 4)
{
    const Wav &W = sym4();
    int h = y.rows, w = y.cols, pad = 1 << levels, ph = (pad - h % pad) % pad, pw = (pad - w % pad) % pad;
    vector<Mat> acc(mults.size());
    for (auto &m : acc) m = Mat::zeros(h, w, CV_32F);
    for (int si = 0; si < NSHIFTS; ++si) {
        int dy = SHIFTS_ALL[si][0], dx = SHIFTS_ALL[si][1];
        Mat P; cv::copyMakeBorder(roll2(y, dy, dx), P, 0, ph, 0, pw, cv::BORDER_REFLECT_101);
        vector<vector<Mat> > lv(levels, vector<Mat>(3));
        Mat cur = P;
        for (int l = 0; l < levels; ++l) {
            Mat A, B, C, D; dwt2(cur, A, B, C, D, W);
            lv[l][0] = B; lv[l][1] = C; lv[l][2] = D; cur = A;
        }
        Mat cA = cur;
        vector<vector<double> > base(levels, vector<double>(3));
        for (int l = 0; l < levels; ++l)
            for (int j = 0; j < 3; ++j) {
                const Mat &b = lv[l][j]; size_t n = b.total();
                double s2 = cv::norm(b, cv::NORM_L2SQR), mx = cv::norm(b, cv::NORM_INF);   // OPT 1
                double sx = std::sqrt(std::max(s2 / n - sigma * sigma, 0.0));
                base[l][j] = sx > 0 ? sigma * sigma / sx : mx;
            }
        for (size_t mi = 0; mi < mults.size(); ++mi) {
            Mat rec = cA;
            for (int l = levels - 1; l >= 0; --l) {
                Mat t0 = softThr(lv[l][0], base[l][0] * mults[mi]);
                Mat t1 = softThr(lv[l][1], base[l][1] * mults[mi]);
                Mat t2 = softThr(lv[l][2], base[l][2] * mults[mi]);
                rec = idwt2(rec, t0, t1, t2, W);
            }
            Mat back = roll2(rec(cv::Rect(0, 0, w, h)), -dy, -dx);
            acc[mi] += back;
        }
    }
    for (auto &m : acc) m *= 1.0f / NSHIFTS;
    return acc;
}

// OPT 2: parallel quantise / dequantise around the (OpenCV) NLM call
static Mat nlmY(const Mat &y, double h_sigma, double ylo, double yhi)
{
    double s = 65535.0 / (yhi - ylo);
    Mat u(y.size(), CV_16U);
    const float *p = fp(y); ushort *q = u.ptr<ushort>(); size_t n = y.total();
    parN(n, [&](size_t i0, size_t i1) {
        for (size_t i = i0; i < i1; ++i) { double v = (p[i] - ylo) * s; q[i] = (ushort)(v < 0 ? 0 : (v > 65535 ? 65535 : v)); }
    });
    Mat o;
    cv::fastNlMeansDenoising(u, o, vector<float>(1, (float)(h_sigma * s)), NLM_TPL, NLM_SRCH, cv::NORM_L1);
    Mat r(y.size(), CV_32F); const ushort *po = o.ptr<ushort>(); float *pr = fp(r);
    parN(n, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) pr[i] = (float)(po[i] / s + ylo); });
    return r;
}
static Mat bilateralY(const Mat &y, double sc) { Mat o; cv::bilateralFilter(y, o, 7, sc, 2.5); return o; }

// ======================================================================================
// RAW loading
// ======================================================================================
struct Cand { double sc; int w, h; bool folded; };
static bool candLess(const Cand &a, const Cand &b)
{
    if (a.sc != b.sc) return a.sc < b.sc;
    if (a.w != b.w) return a.w < b.w;
    return a.h < b.h;
}
static vector<Cand> candidateDims(size_t n, const vector<float> &flat)
{
    double mean = meanOf(flat), sd = stdOf(flat) + 1e-9; (void)mean;
    vector<Cand> rows;
    for (size_t w = 64; w <= n / 64; ++w) {
        if (n % w) continue;
        size_t h = n / w; double asp = (double)w / h;
        if (!(asp >= 0.2 && asp <= 5.0)) continue;
        double sx = 0, sy = 0;
        for (size_t r = 0; r < h; ++r) for (size_t c = 0; c + 1 < w; ++c) sx += std::fabs(flat[r * w + c + 1] - flat[r * w + c]);
        for (size_t r = 0; r + 1 < h; ++r) for (size_t c = 0; c < w; ++c) sy += std::fabs(flat[(r + 1) * w + c] - flat[r * w + c]);
        double sm = 0.5 * (sx / (double)(h * (w - 1)) + sy / (double)((h - 1) * w)) / sd;
        bool folded = false;
        if (w % 2 == 0) {
            size_t hw = w / 2, cnt = h * hw; double sL = 0, sR = 0, sLL = 0, sRR = 0, sLR = 0; size_t m = 0;
            for (size_t j = 0; j < cnt; j += 7) {
                size_t r = j / hw, c = j % hw; double L = flat[r * w + c], R = flat[r * w + hw + c];
                sL += L; sR += R; sLL += L * L; sRR += R * R; sLR += L * R; ++m;
            }
            double cov = sLR / m - (sL / m) * (sR / m), vL = sLL / m - (sL / m) * (sL / m), vR = sRR / m - (sR / m) * (sR / m);
            if (vL > 0 && vR > 0 && cov / std::sqrt(vL * vR) > 0.97) folded = true;
        }
        Cand c; c.sc = sm * (folded ? 1000.0 : 1.0); c.w = (int)w; c.h = (int)h; c.folded = folded; rows.push_back(c);
    }
    std::sort(rows.begin(), rows.end(), candLess);
    if (rows.size() > 5) rows.resize(5);
    return rows;
}

// ======================================================================================
// noise statistics
// ======================================================================================
static Mat hhDb2(const Mat &in)
{
    const Wav &w = db2(); int H = in.rows, W = in.cols, L = w.L;
    Mat D(H / 2, W / 2, CV_32F);
    parRows(H / 2, [&](int k) {
        vector<float> buf(W + L, 0.f);
        for (int n = 0; n < L; ++n) { const float *s = in.ptr<float>((2 * k + n) % H); float gn = w.g[n]; for (int x = 0; x < W; ++x) buf[x] += gn * s[x]; }
        for (int j = 0; j < L; ++j) buf[W + j] = buf[j % W];
        float *pd = D.ptr<float>(k);
        for (int x = 0; x < W / 2; ++x) { float d = 0; for (int n = 0; n < L; ++n) d += w.g[n] * buf[2 * x + n]; pd[x] = d; }
    });
    return D;
}

struct BlockStats { bool ok = false; int Hb = 0, Wb = 0; vector<float> mu, sig; vector<uchar> valid; };

static BlockStats blockNoiseStats(const Mat &img, const Mat *fg, int B = 16)
{
    BlockStats r;
    int h2 = (img.rows / 2) * 2, w2 = (img.cols / 2) * 2;
    Mat im = img(cv::Rect(0, 0, w2, h2)).clone();
    Mat D = hhDb2(im);                                              // diagonal detail (HH)
    int Hb = D.rows / B, Wb = D.cols / B;
    if (Hb < 2 || Wb < 2) return r;
    r.ok = true; r.Hb = Hb; r.Wb = Wb;
    r.mu.assign(Hb * Wb, 0.f); r.sig.assign(Hb * Wb, 0.f); r.valid.assign(Hb * Wb, 1);
    Mat small; cv::resize(im, small, cv::Size(w2 / 2, h2 / 2), 0, 0, cv::INTER_AREA);
    Mat fs;
    if (fg) { Mat fgc = (*fg)(cv::Rect(0, 0, w2, h2)).clone(); cv::resize(fgc, fs, cv::Size(w2 / 2, h2 / 2), 0, 0, cv::INTER_NEAREST); }
    parRows(Hb, [&](int by) {
        vector<float> tmp(B * B);
        for (int bx = 0; bx < Wb; ++bx) {
            int k = 0; double s = 0; bool all = true;
            for (int yy = 0; yy < B; ++yy) {
                const float *pd = D.ptr<float>(by * B + yy) + bx * B, *ps = small.ptr<float>(by * B + yy) + bx * B;
                const uchar *pf = fg ? fs.ptr<uchar>(by * B + yy) + bx * B : nullptr;
                for (int xx = 0; xx < B; ++xx) {
                    tmp[k++] = std::fabs(pd[xx]); s += ps[xx];
                    if (pf && !pf[xx]) all = false;
                }
            }
            std::nth_element(tmp.begin(), tmp.begin() + B * B / 2 - 1, tmp.end());
            float m1 = tmp[B * B / 2 - 1];
            float m2 = *std::min_element(tmp.begin() + B * B / 2, tmp.end());
            r.sig[by * Wb + bx] = 0.5f * (m1 + m2) / 0.6745f;
            r.mu[by * Wb + bx] = (float)(s / (B * B));
            r.valid[by * Wb + bx] = all ? 1 : 0;
        }
    });
    return r;
}

static double robustNoise(const Mat &img, const Mat *fg, double q)
{
    BlockStats r = blockNoiseStats(img, fg);
    if (!r.ok) return stdOf(stridedAll(img, 1));
    vector<float> s;
    for (size_t i = 0; i < r.sig.size(); ++i) if (r.valid[i]) s.push_back(r.sig[i]);
    if (s.empty()) s = r.sig;
    return percentile(s, q);
}

// OPT 7: sigma=12 blur via gaussBig (downsampled); OPT 5: sampled std
static double detailSNR(const Mat &img, const Mat &fg_er)
{
    Mat a, b; cv::GaussianBlur(img, a, cv::Size(), 1.5); b = gaussBig(img, 12.0);
    Mat band = a - b;
    return stdOf(gatherMaskS(band, fg_er)) / std::max(robustNoise(img, &fg_er, 50.0), 1e-12);
}

struct NoiseModel {
    vector<double> cx, fit; double x1 = 1, s1 = 1; vector<float> lut;
    void buildLut(int maxv = 65536) { lut.resize(maxv + 2); for (int i = 0; i < maxv + 2; ++i) lut[i] = (*this)((float)i); }
    float fast(float mu) const {
        if (mu <= 0.f) return lut[0];
        if (mu >= (float)(lut.size() - 2)) return (*this)(mu);
        int i = (int)mu; float f = mu - i; return lut[i] + (lut[i + 1] - lut[i]) * f;
    }
    float operator()(float mu) const { return (float)(interp1(mu, cx, fit) * std::sqrt(std::max(mu / x1, 1.0))); }
    Mat apply(const Mat &m) const
    {
        Mat o(m.size(), CV_32F);
        parRows(m.rows, [&](int y) { const float *s = m.ptr<float>(y); float *d = o.ptr<float>(y); for (int x = 0; x < m.cols; ++x) d[x] = lut.empty() ? (*this)(s[x]) : fast(s[x]); });
        return o;
    }
};

static NoiseModel estimateNoiseModel(const Mat &a, const Mat &fg)
{
    BlockStats r = blockNoiseStats(a, &fg);
    if (!r.ok) DIE("image too small for noise estimation");
    vector<float> m, s;
    for (size_t i = 0; i < r.mu.size(); ++i) if (r.valid[i]) { m.push_back(r.mu[i]); s.push_back(r.sig[i]); }
    if (m.empty()) { m = r.mu; s = r.sig; }
    vector<double> qs(13); for (int i = 0; i < 13; ++i) qs[i] = 100.0 * i / 12.0;
    vector<double> edges = percentiles(m, qs);
    vector<double> cx, cy;
    for (int i = 0; i < 12; ++i) {
        vector<float> mk, sk;
        for (size_t j = 0; j < m.size(); ++j) if (m[j] >= edges[i] && m[j] <= edges[i + 1]) { mk.push_back(m[j]); sk.push_back(s[j]); }
        // FIX 1: median (50th percentile) of block sigmas instead of 25th -> noise is no longer underestimated
        if (mk.size() >= 20) { cx.push_back(medianOf(mk)); cy.push_back(percentile(sk, 50.0)); }
    }
    if (cx.empty()) DIE("noise model: not enough blocks");
    NoiseModel nm; nm.cx = cx;
    vector<double> pv(cx.size());
    double xs = cx.back() > 0 ? cx.back() : 1.0;
    if (cx.size() >= 3) {
        Mat Av((int)cx.size(), 3, CV_64F), bv((int)cx.size(), 1, CV_64F), coef;
        for (size_t i = 0; i < cx.size(); ++i) {
            double u = cx[i] / xs; Av.at<double>((int)i, 0) = u * u; Av.at<double>((int)i, 1) = u; Av.at<double>((int)i, 2) = 1.0; bv.at<double>((int)i) = cy[i];
        }
        cv::solve(Av, bv, coef, cv::DECOMP_SVD);
        for (size_t i = 0; i < cx.size(); ++i) { double u = cx[i] / xs; pv[i] = coef.at<double>(0) * u * u + coef.at<double>(1) * u + coef.at<double>(2); }
    } else pv = cy;
    double run = 0;
    nm.fit.resize(cx.size());
    for (size_t i = 0; i < cx.size(); ++i) { run = std::max(run, std::max(pv[i], 1.0)); nm.fit[i] = run; }
    nm.x1 = cx.back(); nm.s1 = nm.fit.back();
    return nm;
}

// variance stabilising transform
struct VST {
    vector<double> y; double lo = 0, step = 1; int n = 0;
    Mat fwd(const Mat &z) const
    {
        Mat o(z.size(), CV_32F);
        parRows(z.rows, [&](int r) {
            const float *s = z.ptr<float>(r); float *d = o.ptr<float>(r);
            for (int x = 0; x < z.cols; ++x) {
                double t = (s[x] - lo) / step;
                if (t <= 0) d[x] = (float)y[0];
                else if (t >= n - 1) d[x] = (float)y[n - 1];
                else { int i = (int)t; double fr = t - i; d[x] = (float)(y[i] + (y[i + 1] - y[i]) * fr); }
            }
        });
        return o;
    }
    Mat inv(const Mat &yy) const
    {
        Mat o(yy.size(), CV_32F);
        parRows(yy.rows, [&](int r) {
            const float *s = yy.ptr<float>(r); float *d = o.ptr<float>(r);
            for (int x = 0; x < yy.cols; ++x) {
                double v = s[x];
                if (v <= y[0]) { d[x] = (float)lo; continue; }
                if (v >= y[n - 1]) { d[x] = (float)(lo + (n - 1) * step); continue; }
                int i = (int)(std::upper_bound(y.begin(), y.end(), v) - y.begin()) - 1;
                double dy = y[i + 1] - y[i], fr = dy > 0 ? (v - y[i]) / dy : 0.0;
                d[x] = (float)(lo + (i + fr) * step);
            }
        });
        return o;
    }
};
static VST buildVST(const NoiseModel &sg, double lo, double hi, int n = 8192)
{
    VST v; v.n = n; v.lo = lo; v.step = (hi - lo) / (n - 1); v.y.assign(n, 0.0);
    vector<double> is(n);
    for (int i = 0; i < n; ++i) is[i] = 1.0 / (double)sg((float)(lo + i * v.step));
    for (int i = 1; i < n; ++i) v.y[i] = v.y[i - 1] + 0.5 * (is[i] + is[i - 1]) * v.step;
    return v;
}

// ======================================================================================
// image analysis
// ======================================================================================
struct Ctx {
    Mat air, fg, fg_er, D; vector<int> fidx; Filler fill; NoiseModel sigma;
    double cap = 0, d = 0, d_auto = 0, c = 0; bool air_bright = false;
};

static Mat labelTake(const Mat &lab, const vector<uchar> &take)
{
    Mat o(lab.size(), CV_8U); const int *pl = lab.ptr<int>(); uchar *po = o.data; size_t n = lab.total();
    for (size_t i = 0; i < n; ++i) po[i] = take[pl[i]];
    return o;
}

struct AirRes { Mat air; double i0; double frac; std::string method; };

static AirRes segmentAir(const Mat &a)
{
    Mat sm4, sm, sx, sy, g;
    cv::GaussianBlur(a, sm4, cv::Size(), 4); cv::GaussianBlur(a, sm, cv::Size(), 2);
    cv::Sobel(sm, sx, CV_32F, 1, 0, 3); cv::Sobel(sm, sy, CV_32F, 0, 1, 3);
    cv::magnitude(sx, sy, g); g *= 1.0f / 8.0f;
    double i_hi = percentile(stridedAll(sm4, PSTEP), 99.5);
    size_t N = a.total();
    Mat seeds(a.size(), CV_8U);
    { double thr = 0.95 * i_hi; const float *p = fp(sm4); for (size_t i = 0; i < N; ++i) seeds.data[i] = p[i] >= thr ? 1 : 0; }
    seeds = di(er(seeds, 2), 2);
    Mat lab; int n = cv::connectedComponents(seeds, lab, 4, CV_32S);
    if (n > 1) {
        vector<int> sizes(n, 0); const int *pl = lab.ptr<int>();
        for (size_t i = 0; i < N; ++i) ++sizes[pl[i]];
        vector<uchar> keep(n, 0); for (int i = 1; i < n; ++i) keep[i] = sizes[i] >= 2000;
        seeds = labelTake(lab, keep);
    }
    double frac = (double)cv::countNonZero(seeds) / N;
    AirRes R; R.air = Mat::zeros(a.size(), CV_8U); R.method = "none";
    R.i0 = percentile(stridedAll(a, PSTEP), 99.9); R.frac = frac;
    if (frac >= 1e-3) {
        R.i0 = medianOf(gatherMaskS(a, seeds));                                  // OPT 5
        double i0 = R.i0;
        Mat mid(a.size(), CV_8U);
        { const float *ps = fp(sm); for (size_t i = 0; i < N; ++i) mid.data[i] = (ps[i] > 0.3 * i0 && ps[i] < 0.85 * i0) ? 1 : 0; }
        double body_g = anyNZ(mid) ? medianOf(gatherMaskS(g, mid)) : 0.0;       // OPT 5
        Mat core = er(seeds, 6);
        double air_g = anyNZ(core) ? medianOf(gatherMaskS(g, core)) : 0.0;      // OPT 5
        double thr_g = std::max(std::max(0.2 * body_g, 4.0 * air_g), 5.0);
        if (body_g > 0) thr_g = std::min(thr_g, 0.6 * body_g);
        Mat cand(a.size(), CV_8U);
        { const float *ps = fp(sm), *pg = fp(g);
          for (size_t i = 0; i < N; ++i) cand.data[i] = ((ps[i] >= 0.9 * i0 && pg[i] < thr_g) || seeds.data[i]) ? 1 : 0; }
        n = cv::connectedComponents(cand, lab, 4, CV_32S);
        vector<int> sizes(n, 0); const int *pl = lab.ptr<int>();
        for (size_t i = 0; i < N; ++i) ++sizes[pl[i]];
        vector<uchar> take(n, 0);
        for (int i = 1; i < n; ++i) take[i] = sizes[i] >= 1500;
        for (size_t i = 0; i < N; ++i) if (seeds.data[i]) take[pl[i]] = 1;
        take[0] = 0;
        Mat air8 = labelTake(lab, take);
        air8 = er(di(di(air8, 3), 4), 4);
        Mat inv(a.size(), CV_8U); for (size_t i = 0; i < N; ++i) inv.data[i] = air8.data[i] == 0 ? 1 : 0;
        n = cv::connectedComponents(inv, lab, 4, CV_32S);
        vector<uchar> hole(n, 1); hole[0] = 0;
        for (int x = 0; x < a.cols; ++x) { hole[lab.at<int>(0, x)] = 0; hole[lab.at<int>(a.rows - 1, x)] = 0; }
        for (int y = 0; y < a.rows; ++y) { hole[lab.at<int>(y, 0)] = 0; hole[lab.at<int>(y, a.cols - 1)] = 0; }
        pl = lab.ptr<int>();
        for (size_t i = 0; i < N; ++i) R.air.data[i] = (air8.data[i] > 0 || hole[pl[i]]) ? 1 : 0;
        char buf[160]; snprintf(buf, sizeof buf, "seeded region growing (air level %.0f, flatness limit %.0f)", i0, thr_g);
        R.method = buf;
    } else {
        Mat sm3; cv::GaussianBlur(a, sm3, cv::Size(), 3);
        vector<double> lh = percentiles(stridedAll(sm3, PSTEP), vector<double>{0.1, 99.9});
        Mat u8(a.size(), CV_8U); const float *p = fp(sm3);
        for (size_t i = 0; i < N; ++i) { double v = (p[i] - lh[0]) / (lh[1] - lh[0] + 1e-9) * 255.0; u8.data[i] = (uchar)(v < 0 ? 0 : (v > 255 ? 255 : v)); }
        Mat bw; cv::threshold(u8, bw, 0, 255, cv::THRESH_BINARY + cv::THRESH_OTSU);
        double f = (double)cv::countNonZero(bw) / N;
        double s0 = 0, s1 = 0, n0 = 0, n1 = 0, sa = 0, saa = 0;
        for (size_t i = 0; i < N; ++i) { double v = u8.data[i]; sa += v; saa += v * v; if (bw.data[i]) { s1 += v; ++n1; } else { s0 += v; ++n0; } }
        if (f > 0.02 && f < 0.6 && n0 > 0 && n1 > 0) {
            double var = saa / N - (sa / N) * (sa / N), dm = s1 / n1 - s0 / n0;
            double sep = dm * dm / (var + 1e-9) * f * (1 - f);
            if (sep > 0.6 * 0.25) {
                for (size_t i = 0; i < N; ++i) R.air.data[i] = bw.data[i] ? 1 : 0;
                R.method = "otsu"; R.i0 = medianOf(gatherMask(a, R.air));
            }
        }
    }
    return R;
}

static Ctx analyze(const Mat &a, double dark_offset)
{
    Ctx A;
    double mn, mx; cv::minMaxLoc(a, &mn, &mx);
    LOG("size %d x %d px | min %.0f max %.0f", a.cols, a.rows, mn, mx);
    AirRes ar = segmentAir(a);
    A.air = ar.air; A.cap = ar.i0;
    A.fg = Mat(a.size(), CV_8U); parN(a.total(), [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) A.fg.data[i] = ar.air.data[i] ? 0 : 1; });
    // OPT 4: build the nearest-foreground filler concurrently with the noise model
    QFuture<Filler> fFill = QtConcurrent::run([&]() { return buildFiller(A.fg); });
    A.D = taxiDist(A.fg);
    A.fg_er = Mat(a.size(), CV_8U);
    { const float *pd = fp(A.D); parN(a.total(), [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) A.fg_er.data[i] = pd[i] > 12.f ? 1 : 0; }); }
    A.fidx = sampleIdx(A.fg);
    bool anyAir = anyNZ(ar.air);
    vector<int> aidx = anyAir ? sampleIdx(ar.air) : A.fidx;
    A.air_bright = anyAir && medianOf(gather(a, aidx)) > medianOf(gather(a, A.fidx));
    LOG("air level %.0f; body %.1f%% of frame; air mask: %s; polarity: %s", A.cap,
        100.0 * cv::countNonZero(A.fg) / a.total(), ar.method.c_str(), A.air_bright ? "transmission-like" : "attenuation-like");
    A.sigma = estimateNoiseModel(a, A.fg_er);
    A.sigma.buildLut();
    A.fill = fFill.result();
    double p_floor = percentile(gather(a, A.fidx), 0.05);
    double s_floor = A.sigma((float)p_floor);
    A.d_auto = std::max(0.0, p_floor - 2.0 * s_floor);
    A.d = std::isnan(dark_offset) ? A.d_auto : dark_offset;
    A.c = 4.0 * s_floor;
    return A;
}

static Mat detectDefects(const Mat &a, const Ctx &A)
{
    Mat med, dev; cv::medianBlur(a, med, 3); cv::absdiff(a, med, dev);
    Mat sg = A.sigma.apply(med);
    int h = a.rows, w = a.cols;
    vector<cv::Point> cand;
    for (int y = 0; y < h; ++y) {
        const float *pd = dev.ptr<float>(y), *ps = sg.ptr<float>(y); const uchar *pf = A.fg.ptr<uchar>(y);
        for (int x = 0; x < w; ++x) if (pf[x] && pd[x] > 7.0f * ps[x]) cand.push_back(cv::Point(x, y));
    }
    if (cand.empty()) { LOG("hot/dead pixels: 0"); return a; }
    vector<cv::Point> bad;
    vector<float> nb(25);
    for (size_t i = 0; i < cand.size(); ++i) {
        int k = 0;
        for (int dy = -2; dy <= 2; ++dy) for (int dx = -2; dx <= 2; ++dx) {
            int yy = std::min(std::max(cand[i].y + dy, 0), h - 1), xx = std::min(std::max(cand[i].x + dx, 0), w - 1);
            nb[k++] = dev.at<float>(yy, xx);
        }
        std::nth_element(nb.begin(), nb.begin() + 12, nb.end());
        if (dev.at<float>(cand[i].y, cand[i].x) > 6.0f * nb[12] + 1e-6f) bad.push_back(cand[i]);
    }
    int n = (int)bad.size();
    LOG("hot/dead pixels in body: %d", n);
    if (n > 0 && n < 0.001 * cv::countNonZero(A.fg)) {
        Mat out = a.clone();
        for (size_t i = 0; i < bad.size(); ++i) out.at<float>(bad[i].y, bad[i].x) = med.at<float>(bad[i].y, bad[i].x);
        return out;
    }
    return a;
}

// ======================================================================================
// periodic artifact removal
// ======================================================================================
static inline double fftfreq(int i, int n) { return (double)((i < (n - 1) / 2 + 1) ? i : i - n) / n; }

// OPT 3: peak detection on a Welch-averaged spectrum (512x512 tiles, 50% overlap).
// thr is in units of (log-amplitude over local background); 2.5 replaces the old 12.0 (full-size FFT).
static Mat removePeriodic(const Mat &a, const Ctx &A, double thr = 2.5, double rmin = 0.08)
{
    double d = A.d_auto, c = A.c; int h = a.rows, w = a.cols;
    Mat zf = A.fill.apply(a);
    Mat L = logOf(zf, d, c);
    Mat blur6; cv::GaussianBlur(L, blur6, cv::Size(), 6);
    Mat hp = L - blur6;
    for (int y = 0; y < h; ++y) { float *p = hp.ptr<float>(y); const float *pd = A.D.ptr<float>(y); for (int x = 0; x < w; ++x) if (!(pd[x] > 4.f)) p[x] = 0.f; }

    // ---- detection on averaged tile spectrum
    const int TW = 512, TH = TW / 2, wcT = TW / 2 + 1;
    if (h < TW || w < TW) { LOG("stripe/grid pattern: image too small for detection"); return a; }
    Mat winT(TW, TW, CV_32F);
    for (int y = 0; y < TW; ++y) for (int x = 0; x < TW; ++x)
        winT.at<float>(y, x) = (float)(std::sin(CV_PI * (y + 0.5) / TW) * std::sin(CV_PI * (x + 0.5) / TW));
    vector<cv::Point> tp;
    for (int y0 = 0; y0 + TW <= h; y0 += TH) for (int x0 = 0; x0 + TW <= w; x0 += TH) tp.push_back(cv::Point(x0, y0));
    Mat Amp = Mat::zeros(TW, wcT, CV_32F); int ntiles = 0; QMutex mtx0;
    cv::parallel_for_(cv::Range(0, (int)tp.size()), [&](const cv::Range &rg) {
        Mat loc = Mat::zeros(TW, wcT, CV_32F); int cnt = 0;
        for (int i = rg.start; i < rg.end; ++i) {
            Mat tile = hp(cv::Rect(tp[i].x, tp[i].y, TW, TW));
            if (!anyNZ(tile)) continue;
            Mat tw = tile.mul(winT), F; cv::dft(tw, F, cv::DFT_COMPLEX_OUTPUT);
            for (int y = 0; y < TW; ++y) {
                const cv::Vec2f *pf = F.ptr<cv::Vec2f>(y); float *pa = loc.ptr<float>(y);
                for (int x = 0; x < wcT; ++x) pa[x] += std::sqrt(pf[x][0] * pf[x][0] + pf[x][1] * pf[x][1]);
            }
            ++cnt;
        }
        QMutexLocker lk(&mtx0); Amp += loc; ntiles += cnt;
    });
    if (ntiles == 0) { LOG("stripe/grid pattern: none found"); return a; }
    Amp *= 1.0f / ntiles;
    Mat logA, bgl; cv::log(Amp + cv::Scalar::all(1e-9), logA);
    cv::boxFilter(logA, bgl, -1, cv::Size(7, 7), cv::Point(-1, -1), true, cv::BORDER_REPLICATE);
    const float lthr = (float)std::log(thr); const double r2 = rmin * rmin;
    vector<float> rowBest(TW, -1e30f); vector<int> rowX(TW, 0);
    for (int y = 0; y < TW; ++y) {
        double fy = fftfreq(y, TW); const float *pl = logA.ptr<float>(y), *pb = bgl.ptr<float>(y);
        for (int x = 0; x < wcT; ++x) {
            float lr = pl[x] - pb[x]; double fx = (double)x / TW;
            if (lr > lthr && lr > rowBest[y] && fy * fy + fx * fx > r2) { rowBest[y] = lr; rowX[y] = x; }
        }
    }
    int by = 0; for (int y = 1; y < TW; ++y) if (rowBest[y] > rowBest[by]) by = y;
    int bx = rowX[by]; double best = rowBest[by] > -1e29f ? 1.0 : -1.0;
    if (best < 0) { LOG("stripe/grid pattern: none found"); return a; }
    double fy0 = fftfreq(by, TW), fx0 = (double)bx / TW;
    if (fy0 < 0) { fy0 = -fy0; fx0 = -fx0; }
    LOG("stripe/grid pattern found at fy=%+.4f fx=%+.4f; local spectral clipping", fy0, fx0);

    // ---- removal stage (unchanged): local spectral clipping on 256x256 tiles
    const int T = 256, hop = 128, pad = T / 2; const double rad = 0.04, kc = 2.5, kk = 1.5;
    vector<int> regionIdx, ringIdx;
    for (int iy = 0; iy < T; ++iy) for (int ix = 0; ix < T; ++ix) {
        double FY = fftfreq(iy, T), FX = fftfreq(ix, T);
        bool reg = (std::fabs(FY - fy0) <= rad && std::fabs(FX - fx0) <= rad) || (std::fabs(FY + fy0) <= rad && std::fabs(FX + fx0) <= rad);
        bool ring = std::fabs(std::fabs(FY) - fy0) <= rad && std::fabs(FX) >= 0.08 && std::fabs(FX) <= 0.30;
        if (reg) regionIdx.push_back(iy * T + ix);
        if (ring) ringIdx.push_back(iy * T + ix);
    }
    Mat W2(T, T, CV_32F);
    for (int y = 0; y < T; ++y) for (int x = 0; x < T; ++x)
        W2.at<float>(y, x) = (float)(std::sin(CV_PI * (y + 0.5) / T) * std::sin(CV_PI * (x + 0.5) / T));
    Mat hpp, patp; cv::copyMakeBorder(hp, hpp, pad, pad, pad, pad, cv::BORDER_CONSTANT, cv::Scalar(0));
    patp = Mat::zeros(hpp.size(), CV_32F);
    vector<cv::Point> pos;
    for (int y0 = 0; y0 + T <= hpp.rows; y0 += hop) for (int x0 = 0; x0 + T <= hpp.cols; x0 += hop) pos.push_back(cv::Point(x0, y0));
    QMutex mtx;
    cv::parallel_for_(cv::Range(0, (int)pos.size()), [&](const cv::Range &rg) {
        for (int pi = rg.start; pi < rg.end; ++pi) {
            cv::Point p0 = pos[pi];
            Mat tile = hpp(cv::Rect(p0.x, p0.y, T, T));
            if (!anyNZ(tile)) continue;
            Mat tw = tile.mul(W2), Ft; cv::dft(tw, Ft, cv::DFT_COMPLEX_OUTPUT);
            vector<float> rv(ringIdx.size());
            const cv::Vec2f *pF = Ft.ptr<cv::Vec2f>();
            for (size_t i = 0; i < ringIdx.size(); ++i) { const cv::Vec2f &v = pF[ringIdx[i]]; rv[i] = std::sqrt(v[0] * v[0] + v[1] * v[1]); }
            double ref = medianOf(rv) + 1e-12;
            Mat Fp = Mat::zeros(T, T, CV_32FC2); cv::Vec2f *pP = Fp.ptr<cv::Vec2f>(); bool any = false;
            for (size_t i = 0; i < regionIdx.size(); ++i) {
                const cv::Vec2f &v = pF[regionIdx[i]]; double am = std::sqrt((double)v[0] * v[0] + (double)v[1] * v[1]);
                if (am > kc * ref) { double f = 1.0 - kk * ref / am; pP[regionIdx[i]] = cv::Vec2f((float)(v[0] * f), (float)(v[1] * f)); any = true; }
            }
            if (!any) continue;
            Mat inv; cv::dft(Fp, inv, cv::DFT_INVERSE | cv::DFT_SCALE);
            vector<Mat> ch; cv::split(inv, ch);
            Mat res = ch[0].mul(W2);
            QMutexLocker lk(&mtx);
            patp(cv::Rect(p0.x, p0.y, T, T)) += res;
        }
    });
    Mat pat = patp(cv::Rect(pad, pad, w, h)).clone();
    for (size_t i = 0; i < pat.total(); ++i) if (!A.fg.data[i]) fp(pat)[i] = 0.f;
    double rs = stdOf(gatherMaskS(pat, A.fg)), nr = stdOf(gatherMaskS(hp, A.fg));   // OPT 5
    if (rs > 0.35 * nr) { LOG("   safety check failed (could be anatomy) -> pattern removal not applied"); return a; }
    Mat out(a.size(), CV_32F);
    parRows(h, [&](int y) {
        for (int x = 0; x < w; ++x) {
            float v = a.at<float>(y, x);
            if (A.fg.at<uchar>(y, x)) v = (float)(std::exp((double)L.at<float>(y, x) - pat.at<float>(y, x)) - c + d);
            out.at<float>(y, x) = v;
        }
    });
    return out;
}

// per-line offsets and standard errors
// OPT 6: column pass (axis == 0) works on a transposed copy so memory access is contiguous
static void lineOffsets(const Mat &hp0, const Mat &fg0, int axis, bool exact, vector<float> &m, vector<float> &se, vector<uchar> &ok)
{
    Mat hpT, fgT;
    if (axis == 0) { cv::transpose(hp0, hpT); cv::transpose(fg0, fgT); }
    const Mat &hp = (axis == 0) ? hpT : hp0;
    const Mat &fg = (axis == 0) ? fgT : fg0;
    // from here on, "lines" are rows of hp/fg
    int h = hp.rows, w = hp.cols, n = h;
    vector<int> cnt(n, 0);
    parRows(h, [&](int y) { const uchar *pf = fg.ptr<uchar>(y); int c = 0; for (int x = 0; x < w; ++x) c += pf[x] ? 1 : 0; cnt[y] = c; });
    m.assign(n, 0.f); se.assign(n, INFINITY); ok.assign(n, 0);
    double gs = 0;
    if (!exact) {
        vector<float> ab; size_t c = 0;
        for (int y = 0; y < h; ++y) {
            const float *ph = hp.ptr<float>(y); const uchar *pf = fg.ptr<uchar>(y);
            for (int x = 0; x < w; ++x) if (pf[x]) { if (c % PSTEP == 0) ab.push_back(std::fabs(ph[x])); ++c; }
        }
        gs = medianOf(ab) / 0.6745;
    }
    parRows(n, [&](int i) {
        if (cnt[i] < 400) return;
        ok[i] = 1;
        vector<float> v; v.reserve(cnt[i]);
        const float *ph = hp.ptr<float>(i); const uchar *pf = fg.ptr<uchar>(i);
        for (int j = 0; j < w; ++j) if (pf[j]) v.push_back(ph[j]);
        if (exact) {
            float md = (float)medianOf(v);
            vector<float> dv(v.size()); for (size_t k = 0; k < v.size(); ++k) dv[k] = std::fabs(v[k] - md);
            double mad = medianOf(dv);
            m[i] = md; se[i] = (float)(1.2533 * mad / 0.6745 / std::sqrt((double)cnt[i]));
        } else {
            double s = 0, s2 = 0, lim = 3.5 * gs;
            for (size_t k = 0; k < v.size(); ++k) { double c = std::min(std::max((double)v[k], -lim), lim); s += c; s2 += c * c; }
            double mm = s / cnt[i], var = std::max(s2 / cnt[i] - mm * mm, 1e-18);
            m[i] = (float)mm; se[i] = (float)(std::sqrt(var) / std::sqrt((double)cnt[i]));
        }
    });
}

static Mat removeLineNoise(const Mat &a, const Ctx &A, bool exact, double kthr = 1.5, double min_excess = 1.2)
{
    double d = A.d_auto, c = A.c; int h = a.rows, w = a.cols;
    Mat zf = A.fill.apply(a), L = logOf(zf, d, c), bl; cv::GaussianBlur(L, bl, cv::Size(), 4);
    Mat hp = L - bl, Lc; bool changed = false;
    const char *names[2] = {"row", "column"}; int axes[2] = {1, 0};
    for (int k = 0; k < 2; ++k) {
        int axis = axes[k];
        vector<float> off, se; vector<uchar> ok;
        lineOffsets(hp, A.fg, axis, exact, off, se, ok);
        int n = (int)off.size(), nok = 0; double mo = 0, s2 = 0, se2 = 0;
        for (int i = 0; i < n; ++i) if (ok[i]) { ++nok; mo += off[i]; se2 += (double)se[i] * se[i]; }
        if (!nok) continue;
        mo /= nok; for (int i = 0; i < n; ++i) if (ok[i]) s2 += (off[i] - mo) * (off[i] - mo);
        double excess = std::sqrt(s2 / nok) / std::sqrt(se2 / nok);
        if (excess < min_excess) { LOG("%s line noise: %.2fx white-noise expectation -> not activated", names[k], excess); continue; }
        if (!changed) { Lc = L.clone(); changed = true; }
        vector<float> sh(n, 0.f);
        for (int i = 0; i < n; ++i) if (ok[i]) {
            double v = std::max(std::fabs((double)off[i]) - kthr * se[i], 0.0); sh[i] = (float)(off[i] < 0 ? -v : v);
        }
        for (int y = 0; y < h; ++y) { float *p = Lc.ptr<float>(y); for (int x = 0; x < w; ++x) p[x] -= sh[axis == 1 ? y : x]; }
        LOG("%s line noise: removed (spread %.2fx white-noise expectation)", names[k], excess);
    }
    if (!changed) return a;
    Mat out(a.size(), CV_32F);
    parRows(h, [&](int y) {
        for (int x = 0; x < w; ++x) {
            float v = a.at<float>(y, x);
            if (A.fg.at<uchar>(y, x)) v = (float)(std::exp((double)Lc.at<float>(y, x)) - c + d);
            out.at<float>(y, x) = v;
        }
    });
    return out;
}

// ======================================================================================
// x-ray enhancement
// ======================================================================================
struct Args {
    QString raw, outdir = "."; int width = 0, height = 0, header = 0; bool strict = false, big = false;
    double dark = NAN, denoise_k = NAN, denoise_scale = 1.5 /*FIX 6*/, thickness_k = 0.75, bone_gain = NAN, soft_reduce = 0.0;
    double gamma = NAN, clahe = NAN, sharpen = NAN, clahe_clip = 2.0 /*FIX 5*/, smooth = 1.0 /*FIX 6*/;
    bool thickness_k_user = false, no_denoise = false, remove_chain = false;
    double target_median = 0.30; bool auto_gamma = true;   // BRT 2
    std::string speed = "fast", denoiser = "wavelet", view = "auto";
};
static double pick(double v, double t, double s, bool thick) { return std::isnan(v) ? (thick ? t : s) : v; }

static Mat maskedLowpass(const Mat &L, const Mat &fg, double sigma, int ds = 8)
{
    int h = L.rows, w = L.cols, sh = std::max(h / ds, 8), sw = std::max(w / ds, 8);
    Mat Lm(h, w, CV_32F), fgf(h, w, CV_32F);
    parN(L.total(), [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) { float f = fg.data[i] ? 1.f : 0.f; fp(fgf)[i] = f; fp(Lm)[i] = fp(L)[i] * f; } });
    Mat Ls, ws, num, den, B, out;
    cv::resize(Lm, Ls, cv::Size(sw, sh), 0, 0, cv::INTER_AREA);
    cv::resize(fgf, ws, cv::Size(sw, sh), 0, 0, cv::INTER_AREA);
    double s = sigma / ds;
    cv::GaussianBlur(Ls, num, cv::Size(), s); cv::GaussianBlur(ws, den, cv::Size(), s);
    B.create(sh, sw, CV_32F);
    for (size_t i = 0; i < B.total(); ++i) fp(B)[i] = fp(num)[i] / std::max(fp(den)[i], 1e-3f);
    cv::resize(B, out, cv::Size(w, h), 0, 0, cv::INTER_CUBIC);
    return out;
}
static Mat gaussBig(const Mat &x, double sigma)
{
    Mat o;
    if (sigma <= 6) { cv::GaussianBlur(x, o, cv::Size(), sigma, sigma, cv::BORDER_REPLICATE); return o; }
    int f = std::max((int)std::pow(2.0, std::floor(std::log2(sigma / 3.0))), 1);
    int h = x.rows, w = x.cols; Mat small = x;
    if (f != 1) cv::resize(x, small, cv::Size(std::max(w / f, 8), std::max(h / f, 8)), 0, 0, cv::INTER_AREA);
    Mat sb; cv::GaussianBlur(small, sb, cv::Size(), sigma / f, sigma / f, cv::BORDER_REPLICATE);
    cv::resize(sb, o, cv::Size(w, h), 0, 0, cv::INTER_LINEAR);
    return o;
}
static inline float shoulderf(float x, float k)
{
    float lo = 1.f - k, hi = k, w = 1.f - k;
    if (x > hi) return hi + w * std::tanh((x - hi) / w);
    if (x < lo) return lo - w * std::tanh((lo - x) / w);
    return x;
}

static Mat removeChain(const Mat &att, const Mat &fg, double scale, int size = 31, double thr = 0.12)
{
    auto tophat = [&](int k) {
        k = std::max(((int)(k * scale)) | 1, 5); Mat o;
        cv::morphologyEx(att, o, cv::MORPH_TOPHAT, cv::getStructuringElement(cv::MORPH_ELLIPSE, cv::Size(k, k))); return o;
    };
    Mat t1 = tophat(size), t2 = tophat(size * 3), g = gaussBig(att, 60.0 * scale);
    size_t N = att.total(); Mat m(att.size(), CV_8U);
    for (size_t i = 0; i < N; ++i) {
        bool m1 = fp(t1)[i] > thr && fg.data[i];
        bool m2 = fp(t2)[i] > 0.30 && (fp(att)[i] - fp(g)[i]) > 0.30 && fg.data[i];
        m.data[i] = (m1 || m2) ? 1 : 0;
    }
    Mat lab, stats, cen; int n = cv::connectedComponentsWithStats(m, lab, stats, cen, 4, CV_32S);
    vector<uchar> keep(n, 0); for (int i = 1; i < n; ++i) keep[i] = stats.at<int>(i, cv::CC_STAT_AREA) > 60;
    m = di(labelTake(lab, keep), 5);
    double mn, amax; cv::minMaxLoc(att, &mn, &amax); amax += 1e-9;
    Mat a16(att.size(), CV_16U);
    for (size_t i = 0; i < N; ++i) { double v = fp(att)[i] / amax * 65535.0; a16.ptr<ushort>()[i] = (ushort)(v < 0 ? 0 : (v > 65535 ? 65535 : v)); }
    Mat inp; cv::inpaint(a16, m, inp, 7, cv::INPAINT_TELEA);
    LOG("chain/metal removal: %d px inpainted", cv::countNonZero(m));
    Mat out(att.size(), CV_32F);
    for (size_t i = 0; i < N; ++i) fp(out)[i] = (float)(inp.ptr<ushort>()[i] / 65535.0 * amax);
    return out;
}

static Mat xrayCore(const Mat &att, const Mat &fg, const vector<int> &fidx, const Args &args, double sc, bool thick,
                    double &sig_t_o, double &k_t_o, vector<double> &gains_o)
{
    int h = att.rows, w = att.cols; size_t N = att.total();
    // Current standard-profile tuning (FIX 3/4/5 lineage). Thick profile unchanged.
    double bone_gain = pick(args.bone_gain, 1.0, 1.0, thick), gamma = pick(args.gamma, 1.1, 1.9, thick);
    double clahe_s = pick(args.clahe, 1.0, 0.20, thick), sharpen_s = pick(args.sharpen, 0.0, 2.2, thick);

    double sig_t, k_t; vector<double> gains;
    if (thick) { sig_t = 250.0 * sc; k_t = args.thickness_k_user ? args.thickness_k : 0.55; double g[6] = {1.0, 1.5, 2.2, 2.4, 2.0, 1.4}; gains.assign(g, g + 6); }
    else { sig_t = 140.0 * sc; k_t = args.thickness_k; double g[6] = {0.9, 1.3, 3.0, 3.1, 2.4, 1.4}; gains.assign(g, g + 6); }
    for (auto &g : gains) g *= bone_gain;
    sig_t_o = sig_t; k_t_o = k_t; gains_o = gains;

    Mat low = maskedLowpass(att, fg, sig_t, 8);
    double lowMean = meanOf(gather(low, fidx));
    Mat a(h, w, CV_32F);
    parN(N, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) fp(a)[i] = fp(att)[i] - (float)(k_t * (fp(low)[i] - lowMean)) * (fg.data[i] ? 1.f : 0.f); });
    double sigv[6] = {1.2, 2.5, 5, 10, 22, 48};
    double scale = stdOf(gather(a, fidx)), lim = 0.35 * scale;
    Mat prev = a, out = Mat::zeros(h, w, CV_32F);
    for (int b = 0; b < 6; ++b) {
        Mat cur = gaussBig(prev, sigv[b] * sc);
        float gk = (float)(gains[b] / lim), lf = (float)lim;
        parRows(h, [&](int y) {
            const float *pp = prev.ptr<float>(y), *pc = cur.ptr<float>(y); float *po = out.ptr<float>(y);
            for (int x = 0; x < w; ++x) po[x] += fastTanh((pp[x] - pc[x]) * gk) * lf;   // OPT 7
        });
        prev = cur;
    }
    double cm = meanOf(gather(prev, fidx));
    parN(N, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) fp(out)[i] += (float)(cm + (1.0 - args.soft_reduce) * (fp(prev)[i] - cm)); });

    vector<float> sv = gather(out, fidx); double lo, hi;
    if (thick) {
        double cut = percentile(sv, 15.0); vector<float> s2; for (float v : sv) if (v > cut) s2.push_back(v);
        vector<double> q = percentiles(s2, vector<double>{5.0, 99.7}); lo = q[0]; hi = q[1];
    } else { vector<double> q = percentiles(sv, vector<double>{0.3, 99.7}); lo = q[0]; hi = q[1]; }
    Mat n(h, w, CV_32F);
    parN(N, [&](size_t i0, size_t i1) {
        for (size_t i = i0; i < i1; ++i) {
            float v = (float)((fp(out)[i] - lo) / (hi - lo + 1e-9));
            v = shoulderf(v, 0.97f); fp(n)[i] = std::min(std::max(v, 0.f), 1.f);
        }
    });
    if (thick) {
        vector<double> qs(1001); for (int i = 0; i < 1001; ++i) qs[i] = 100.0 * i / 1000.0;
        vector<double> v = percentiles(gather(n, fidx), qs), ys(1001);
        double run = -1e300;
        for (int i = 0; i < 1001; ++i) { run = std::max(run, v[i] + i * 1e-7); v[i] = run; ys[i] = i / 1000.0; }
        for (size_t i = 0; i < N; ++i) { float eq = (float)interp1(fp(n)[i], v, ys); fp(n)[i] = 0.75f * fp(n)[i] + 0.25f * eq; }
    }
    // BRT 2: auto gamma (standard profile, only when --gamma not given): darken bright images, never brighten
    if (!thick && std::isnan(args.gamma) && args.auto_gamma) {
        double med = medianOf(gather(n, fidx));
        double g0 = gamma;
        if (med > 0.02 && med < 0.98) gamma = std::min(std::max(std::log(args.target_median) / std::log(med), g0), 2.6);
        LOG("auto gamma: body median %.3f (before gamma) -> gamma %.2f (default %.2f, target median %.2f)", med, gamma, g0, args.target_median);
    }
    parN(N, [&](size_t i0, size_t i1) {
        if (gamma == 2.0) for (size_t i = i0; i < i1; ++i) fp(n)[i] *= fp(n)[i];
        else for (size_t i = i0; i < i1; ++i) fp(n)[i] = std::pow(fp(n)[i], (float)gamma);
    });
    if (clahe_s > 0) {
        int nt = (int)std::min(std::max(std::nearbyint(std::max(h, w) / 256.0), 4.0), 16.0);
        double clip = thick ? 2.5 : args.clahe_clip;
        Mat u16(h, w, CV_16U);
        parN(N, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) { double v = fp(n)[i] * 65535.0 + 0.5; u16.ptr<ushort>()[i] = (ushort)(v < 0 ? 0 : (v > 65535 ? 65535 : v)); } });
        cv::Ptr<cv::CLAHE> cl = cv::createCLAHE(clip, cv::Size(nt, nt)); Mat o; cl->apply(u16, o);
        parN(N, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) fp(n)[i] = (float)((1.0 - clahe_s) * fp(n)[i] + clahe_s * (o.ptr<ushort>()[i] / 65535.0)); });
    }
    if (sharpen_s > 0) {
        Mat bl; cv::GaussianBlur(n, bl, cv::Size(), 1.5);
        // FIX 4: coring threshold 0.012 -> 0.03 so grain-level detail is not sharpened
        // OPT 7: parallel
        const float ss = (float)sharpen_s;
        parN(N, [&](size_t i0, size_t i1) {
            for (size_t i = i0; i < i1; ++i) { float dd = fp(n)[i] - fp(bl)[i], t = std::max(std::fabs(dd) - 0.03f, 0.f); fp(n)[i] += ss * (dd < 0 ? -t : t); }
        });
        if (!thick) parN(N, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) fp(n)[i] = shoulderf(fp(n)[i], 0.95f); });   // BRT 3
    }
    if (thick) {
        Mat bl; cv::GaussianBlur(n, bl, cv::Size(), 2.0);
        for (size_t i = 0; i < N; ++i) { float d = fp(n)[i] - fp(bl)[i]; if (std::fabs(d) < 0.004f) d = 0.f; fp(n)[i] += 0.6f * d; }
    }
    parN(N, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) fp(n)[i] = std::min(std::max(fp(n)[i], 0.f), 1.f); });
    return n;
}

static Mat xrayEnhance(const Mat &L, const Ctx &A, const Args &args)
{
    int h = L.rows, w = L.cols; double sc = std::max(h, w) / 3072.0; size_t N = L.total();
    Mat att(h, w, CV_32F);
    if (A.air_bright) {
        double ref = std::log(std::max(A.cap - A.d + A.c, 1.0));
        parN(N, [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) fp(att)[i] = std::max((float)(ref - fp(L)[i]), -0.05f); });
    } else {
        float mn = 1e30f; for (int i : A.fidx) mn = std::min(mn, fp(L)[i]);
        for (size_t i = 0; i < N; ++i) fp(att)[i] = fp(L)[i] - mn;
    }
    if (args.remove_chain) att = removeChain(att, A.fg, sc);
    bool thick = args.view == "thick";
    double st, kt; vector<double> gn;
    if (args.view == "auto") {
        // BRT 4: half-resolution probe (quarter-res could flip the profile)
        int h2 = std::max(h / 2, 64), w2 = std::max(w / 2, 64); Mat ah, fgh;
        cv::resize(att, ah, cv::Size(w2, h2), 0, 0, cv::INTER_AREA);
        cv::resize(A.fg, fgh, cv::Size(w2, h2), 0, 0, cv::INTER_NEAREST);
        Args probe = args; probe.clahe = 0.0; probe.gamma = 1.1; probe.bone_gain = 1.0; probe.sharpen = 0.0;
        vector<int> hidx = sampleIdx(fgh);
        Mat pr = xrayCore(ah, fgh, hidx, probe, sc / 2.0, false, st, kt, gn);
        vector<double> q = percentiles(gather(pr, hidx), vector<double>{25.0, 75.0});
        double iqr = q[1] - q[0], body = (double)cv::countNonZero(A.fg) / N;
        thick = iqr < 0.15 && body < 0.85;
        LOG("view check: inter-quartile grey width %.2f, body fills %.0f%% -> %s profile", iqr, body * 100, thick ? "THICK/lateral" : "standard (AP/PA)");
    }
    Mat n = xrayCore(att, A.fg, A.fidx, args, sc, thick, st, kt, gn);
    {   // BRT 1: output brightness report
        vector<float> ov = gather(n, A.fidx);
        vector<double> q = percentiles(ov, vector<double>{1, 25, 50, 75, 99});
        size_t sat = 0; for (float v : ov) if (v >= 0.995f) ++sat;
        LOG("output body: p1 %.3f p25 %.3f median %.3f p75 %.3f p99 %.3f | near-white %.2f%%",
            q[0], q[1], q[2], q[3], q[4], 100.0 * sat / std::max<size_t>(ov.size(), 1));
    }
    LOG("profile '%s': thickness sigma %.0f px k %.2f; band gains [%.2f, %.2f, %.2f, %.2f, %.2f, %.2f]",
        thick ? "thick" : "standard", st, kt, gn[0], gn[1], gn[2], gn[3], gn[4], gn[5]);
    return n;
}

// ======================================================================================
// main
// ======================================================================================
int main(int argc, char **argv)
{
    QCoreApplication app(argc, argv);
    cv::setNumThreads(std::max(1, QThread::idealThreadCount()));
    g_t0.start();
    LOG("OpenCV threads: %d", cv::getNumThreads());
    QCommandLineParser P;
    P.setApplicationDescription("Bone enhancement (xray level, RAW out)");
    P.addHelpOption();
    P.addPositionalArgument("raw", "input .raw (uint16)");
    auto opt = [&](const char *n, const char *def = "") { P.addOption(QCommandLineOption(n, n, "v", def)); };
    opt("width"); opt("height"); opt("endian", "little"); opt("header-bytes", "0"); opt("outdir", ".");
    opt("dark-offset"); opt("seed"); opt("level", "xray"); opt("speed", "fast"); opt("denoiser", "wavelet");
    opt("denoise-k"); opt("denoise-scale", "1.5"); opt("view", "auto"); opt("thickness-k", "0.75");   // FIX 6
    opt("bone-gain"); opt("soft-reduce", "0.0"); opt("gamma"); opt("clahe"); opt("sharpen");
    opt("clahe-clip", "2.0"); opt("smooth", "1.0"); opt("target-median", "0.30");                                                    // FIX 5 / FIX 6
    P.addOption(QCommandLineOption("strict-dims")); P.addOption(QCommandLineOption("no-denoise"));
    P.addOption(QCommandLineOption("no-auto-gamma")); P.addOption(QCommandLineOption("remove-chain")); P.addOption(QCommandLineOption("quiet"));
    P.process(app);

    Args args;
    QStringList pos = P.positionalArguments();
    args.raw = pos.isEmpty() ? QString("Originalproj_0.raw") : pos[0];
    auto num = [&](const char *n, double def) { return P.isSet(n) ? P.value(n).toDouble() : def; };
    args.width = P.isSet("width") ? P.value("width").toInt() : 0;
    args.height = P.isSet("height") ? P.value("height").toInt() : 0;
    args.strict = P.isSet("strict-dims"); args.big = P.value("endian") == "big";
    args.header = P.value("header-bytes").toInt(); args.outdir = P.value("outdir");
    args.dark = num("dark-offset", NAN); args.denoise_k = num("denoise-k", NAN); args.denoise_scale = num("denoise-scale", 1.5);
    args.thickness_k = num("thickness-k", 0.75); args.thickness_k_user = P.isSet("thickness-k");
    args.bone_gain = num("bone-gain", NAN); args.soft_reduce = num("soft-reduce", 0.0);
    args.gamma = num("gamma", NAN); args.clahe = num("clahe", NAN); args.sharpen = num("sharpen", NAN);
    args.clahe_clip = num("clahe-clip", 1.5); args.smooth = num("smooth", 1.0);   // NOTE: fallback 1.5 kept as in the original (see FIX 5 comment: 2.0)
    args.target_median = num("target-median", 0.30); args.auto_gamma = !P.isSet("no-auto-gamma");
    args.no_denoise = P.isSet("no-denoise"); args.remove_chain = P.isSet("remove-chain");
    args.speed = P.value("speed").toStdString(); args.denoiser = P.value("denoiser").toStdString(); args.view = P.value("view").toStdString();
    QUIET = P.isSet("quiet");
    bool exact = args.speed == "exact";
    PSTEP = exact ? 1 : 7;
    // OPT 2: fast mode NLM template 5 / search 9
    if (args.speed == "exact") { NLM_TPL = 7; NLM_SRCH = 21; }
    else if (args.speed == "turbo") { NLM_TPL = 5; NLM_SRCH = 7; }
    else { NLM_TPL = 5; NLM_SRCH = 9; }
    // OPT 1: 2 cycle-spin shifts unless exact
    NSHIFTS = exact ? 4 : 2;
    if (args.denoiser == "auto") { LOG("note: --denoiser auto is not ported; using wavelet"); args.denoiser = "wavelet"; }
    QDir().mkpath(args.outdir);

    // ---------------- load RAW
    QFile f(args.raw);
    if (!f.open(QIODevice::ReadOnly)) DIE("cannot open " + args.raw.toStdString());
    QByteArray all = f.readAll(); f.close();
    qint64 size = all.size();
    if (size - args.header < 0 || (size - args.header) % 2) DIE("file size minus header is not a multiple of 2 bytes (uint16)");
    size_t n_pix = (size_t)(size - args.header) / 2;
    const uchar *raw = reinterpret_cast<const uchar *>(all.constData()) + args.header;
    auto rd = [&](size_t i) -> float { return args.big ? (float)((raw[2 * i] << 8) | raw[2 * i + 1]) : (float)(raw[2 * i] | (raw[2 * i + 1] << 8)); };
    int width = args.width, height = args.height;
    if (width <= 0 || height <= 0) {
        vector<float> flat(n_pix); for (size_t i = 0; i < n_pix; ++i) flat[i] = rd(i);
        vector<Cand> cands = candidateDims(n_pix, flat), good;
        for (auto &c : cands) if (!c.folded) good.push_back(c);
        bool confident = good.size() >= 2 && good[0].sc < 0.05 * good[1].sc;
        if (!args.strict && !good.empty() && confident) { width = good[0].w; height = good[0].h; LOG("Automatic size: %d x %d", width, height); }
        else {
            for (auto &c : cands) LOG("   --width %d --height %d   score %.4f%s", c.w, c.h, c.sc, c.folded ? "  (folded)" : "");
            DIE("Re-run with --width and --height (and --endian / --header-bytes if needed).");
        }
    }
    if ((size_t)width * height != n_pix) DIE("width*height does not match the number of samples in the file");
    QByteArray hdr = all.left(args.header);
    Mat a0(height, width, CV_32F);
    parRows(height, [&](int y) { float *p = a0.ptr<float>(y); for (int x = 0; x < width; ++x) p[x] = rd((size_t)y * width + x); });
    all.clear(); all.squeeze();
    TIC("load");

    double dark = std::isnan(args.dark) ? 0.0 : args.dark;      // xray level: plain Beer-Lambert log
    Ctx A = analyze(a0, dark);
    TIC("analysis (air mask, noise model)");

    // OPT 4: final-fade distance transform only depends on A.fg -> compute in the background
    QFuture<Mat> fDist = QtConcurrent::run([&]() -> Mat {
        Mat dd;
        if (cv::countNonZero(A.fg) < (int)A.fg.total()) cv::distanceTransform(A.fg, dd, cv::DIST_L2, cv::DIST_MASK_5);
        else dd = Mat(A.fg.size(), CV_32F, cv::Scalar(1e6));
        return dd;
    });

    Mat L0 = logOf(A.fill.apply(a0), A.d, A.c);
    QFuture<double> fSNR = QtConcurrent::run([&]() { return detailSNR(L0, A.fg_er); });
    Mat a1 = detectDefects(a0, A);                  TIC("hot pixels");
    Mat a2 = removePeriodic(a1, A);                 TIC("stripe/grid check");
    a2 = removeLineNoise(a2, A, exact);             TIC("line noise");

    // ---------------- denoise (variance-stabilised)
    Mat zf = A.fill.apply(a2), z_dn;
    double ds0 = fSNR.result();
    // FIX 2: gate raised 30 -> 60 so moderately noisy images are still denoised before enhancement
    bool do_dn = ds0 < 60.0 && !args.no_denoise;
    LOG("detail SNR %.1f (denoise if < 60) -> denoising %s", ds0, do_dn ? "ON" : "OFF");
    if (do_dn) {
        double lo = percentile(stridedAll(zf, PSTEP), 0.01) - 50.0, mx, mn; cv::minMaxLoc(zf, &mn, &mx);
        double hi = mx + 50.0;
        VST vst = buildVST(A.sigma, std::max(lo, 1.0), hi);
        Mat y = vst.fwd(zf);
        vector<double> yq = percentiles(gather(y, A.fidx), vector<double>{0.01, 99.99});
        double ylo = yq[0] - 5.0, yhi = yq[1] + 5.0;
        TIC("VST");
        Mat sg = A.sigma.apply(zf), w_lo(zf.size(), CV_32F);
        parN(zf.total(), [&](size_t i0, size_t i1) {
            for (size_t i = i0; i < i1; ++i) {
                double rel = fp(sg)[i] / std::max((double)fp(zf)[i] - A.d_auto + A.c, 1.0);
                fp(w_lo)[i] = smoothstepf((float)((rel - 0.010) / (0.025 - 0.010))) * (A.fg.data[i] ? 1.f : 0.f);
            }
        });
        double wmn, wmx; cv::minMaxLoc(w_lo, &wmn, &wmx);
        bool need_strong = wmx > 0.05;
        std::string mname = args.denoiser;
        double k = std::isnan(args.denoise_k) ? (mname == "wavelet" ? 1.0 : (mname == "nlm" ? 1.2 : 2.5)) : args.denoise_k;
        double strong_m = 2.0 * args.denoise_scale;
        Mat y_dn, y_strong;
        if (mname == "wavelet") {
            vector<double> mults(1, k * args.denoise_scale); if (need_strong) mults.push_back(strong_m);
            vector<Mat> res = waveletMulti(y, mults);
            y_dn = res[0]; if (need_strong) y_strong = res[1];
        } else {
            y_dn = (mname == "nlm") ? nlmY(y, k, ylo, yhi) : bilateralY(y, k);
            if (need_strong) y_strong = waveletMulti(y, vector<double>(1, strong_m))[0];
        }
        TIC("%s denoise%s", mname.c_str(), need_strong ? " + strong" : "");
        if (need_strong) parN(y_dn.total(), [&](size_t i0, size_t i1) { for (size_t i = i0; i < i1; ++i) fp(y_dn)[i] = fp(y_dn)[i] * (1.f - fp(w_lo)[i]) + fp(y_strong)[i] * fp(w_lo)[i]; });
        if (args.smooth > 0) {
            Mat y_nlm = nlmY(y_dn, 1.6, ylo, yhi);
            parN(y_dn.total(), [&](size_t i0, size_t i1) {
                for (size_t i = i0; i < i1; ++i) {
                    float wS = (float)std::min(std::max(args.smooth * (0.3 + 0.7 * fp(w_lo)[i]), 0.0), 1.0);
                    fp(y_dn)[i] = fp(y_dn)[i] * (1.f - wS) + fp(y_nlm)[i] * wS;
                }
            });
            TIC("NLM smooth (template %d, search %d)", NLM_TPL, NLM_SRCH);
        }
        z_dn = vst.inv(y_dn);
    } else z_dn = zf;
    Mat L = logOf(z_dn, A.d, A.c);

    // ---------------- enhancement
    Mat xn = xrayEnhance(L, A, args);
    TIC("xray enhancement");

    // ---------------- reconstruction
    Mat distAir = fDist.result();                                   // OPT 4 (computed in background)
    vector<quint16> out16(n_pix);
    parN(n_pix, [&](size_t i0, size_t i1) {
        for (size_t i = i0; i < i1; ++i) {
            double wf = smoothstepf(fp(distAir)[i] / 6.f);
            double v = std::nearbyint(wf * (65535.0 * fp(xn)[i]));
            out16[i] = (quint16)(v < 0 ? 0 : (v > 65535 ? 65535 : v));
        }
    });
    auto byteAt = [&](int j) -> int {            // byte j of the first two samples as stored on disk
        int s = j / 2; quint16 v = out16[s]; bool hiFirst = args.big; int isHi = (j % 2 == 0) == hiFirst;
        return isHi ? (v >> 8) : (v & 0xFF);
    };
    if (hdr.isEmpty() && n_pix >= 2) {           // keep ImageJ from mistaking the RAW for a DICOM
        int b0 = byteAt(0), b1 = byteAt(1), b3 = byteAt(3);
        if ((b0 == 2 || b0 == 8) && b1 == 0 && b3 == 0) out16[0] += 1;
    }
    QByteArray bytes; bytes.resize((int)(n_pix * 2));
    uchar *pb = reinterpret_cast<uchar *>(bytes.data());
    const bool bigE = args.big;
    parN(n_pix, [&](size_t i0, size_t i1) {      // OPT 7: parallel packing
        for (size_t i = i0; i < i1; ++i) {
            quint16 v = out16[i];
            if (bigE) { pb[2 * i] = v >> 8; pb[2 * i + 1] = v & 0xFF; } else { pb[2 * i] = v & 0xFF; pb[2 * i + 1] = v >> 8; }
        }
    });
    QString outPath = QDir(args.outdir).filePath("Enhanced_Bone.raw");
    QFile fo(outPath);
    if (!fo.open(QIODevice::WriteOnly)) DIE("cannot write " + outPath.toStdString());
    if (!hdr.isEmpty()) fo.write(hdr);
    fo.write(bytes); fo.close();
    TIC("write %s (%lld bytes)", outPath.toLocal8Bit().constData(), (long long)QFileInfo(outPath).size());
    LOG("TOTAL %.2fs", g_t0.nsecsElapsed() * 1e-9);
    return 0;
}

```
