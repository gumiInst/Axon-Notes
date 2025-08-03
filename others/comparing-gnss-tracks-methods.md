# Comparing GNSS Trajectories: Practical Challenges and Solutions

## Problems in Comparing GNSS Trajectories

Ideally, two GNSS receivers recording their positions at identical times would allow easy, one-to-one point matching via timestamps, making spatial error calculation straightforward. However, in practice, real-world GNSS data often does not align so perfectly:

- **Timestamp Mismatch:** Devices rarely record at exactly the same times due to differences in sampling rates, clock drift, or missing data.
- **Irregular Sampling:** Even a single device may log positions at uneven intervals.
- **Result:** Simple point-to-point matching using timestamps becomes impractical, complicating meaningful trajectory comparisons.

## Methods to Overcome These Problems

To fairly compare two GNSS sequences, points must be paired even when their timestamps do not align. Common approaches include:

### 1. Dynamic Time Warping (DTW) Alignment

- **What it does:** DTW is an algorithm that aligns two sequences by flexibly matching points based on similarity in trajectory shape, regardless of timestamp differences or sampling rates.
- **Outcome:** This pairing allows calculation of spatial error for each matched pair, characterizing the positional discrepancy between the two tracks.
- **Note:** DTW is especially useful when paths cover similar shapes at different rates but may not guarantee best spatial matching for every scenario.

### 2. Interpolation to Uniform Timestamps

- **What it does:** Interpolation is used to estimate each receiver's position at a regularly spaced set of common time points (e.g., every second), even if the original data was not captured at those times.
- **Outcome:** This produces two new, synchronized sequences with matching timestamps, enabling straightforward point-to-point error calculation.
- **Note:** Interpolation (whether linear, spline, etc.) can introduce its own errors, especially when movement between original points is not linear.

---

**In summary:**  
Real-world GNSS trajectory comparisons are complicated by timestamp mismatches and irregular sampling. Methods like Dynamic Time Warping and interpolation enable meaningful pairing of points for error analysis, each with their own advantages and limitations.
