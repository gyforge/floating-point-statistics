# From Unexpected Correlation to Floating-Point Arithmetic in NumPy and pandas

This project began with an unexpected result while implementing lagged Pearson
correlation: exactly constant input vectors could produce finite correlation
values, including `+1` or `-1`, even though Pearson correlation is
mathematically undefined for zero-variance inputs.

Understanding that result led to a deeper investigation of floating-point
mean-centering and standard deviation. Parts I and II document that branch,
including differences between NumPy and pandas and the role of floating-point
precision and reduction algorithms.

Notebook 03 returns to the original correlation problem. It shows how tiny
numerical residuals created during mean-centering can produce apparently perfect
positive or negative correlations, compares several library paths, and adds a
practical constant-input safeguard.

The aim of the notebooks is not to propose replacement implementations for
NumPy or pandas. They document the debugging path, reproduce the numerical
behavior, and connect it to practical data-analysis safeguards.

## Notebooks

1. [Part I — Floating-point arithmetic and rounding error](notebooks/01_Floating_point_arithmetic_rounding_error_Part-I.ipynb)  
   Reproduces non-zero standard deviations for exactly constant arrays and compares NumPy with the native pandas path.

2. [Part II — Why pandas returns zero for float32 constant arrays](notebooks/02_Floating_point_arithmetic_rounding_error_Part-II.ipynb)  
   Traces the native pandas 3.0.5 standard-deviation path and shows how float64 accumulation changes the result for constant float32 arrays. Optional Bottleneck acceleration is outside the scope of the notebook.

3. [Pearson correlation and constant inputs](notebooks/03_Floating_point_rounding_error_on_Pearson_correlation.ipynb)  
   Returns to the original correlation problem. It reproduces finite `+1` and `-1` correlations for exactly constant inputs, compares `np.corrcoef()`, pandas `Series.corr()`, pandas `DataFrame.corr()`, and SciPy `pearsonr()`, and shows why changing the reduction length through lagging can change the sign of the numerical artifact. It finishes with an explicit constant-input guard for practical correlation workflows.

## Main findings

- Exactly constant floating-point data can produce a small non-zero computed variance or standard deviation because the calculated mean is not always identical to the stored value.
- The result depends on dtype, reduction length, working precision, and the implementation path used by the library.
- Promoting a stored float32 value to float64 is exact, but later arithmetic still follows the precision and reduction rules of the working dtype.
- Pearson correlation is undefined when either input is constant. If floating-point mean-centering creates uniform non-zero residuals, the normalized residual vectors can nevertheless produce an apparent correlation of `+1` or `-1`.
- For exact constant detection, checking the stored values directly is more reliable than using a numerically calculated standard deviation as the test.

## Reported issues

- [NumPy #32446](https://github.com/numpy/numpy/issues/32446) — Closed as not planned. The observed `np.corrcoef()` behavior remains unchanged.
- [pandas #67023](https://github.com/pandas-dev/pandas/issues/67023) — Closed. The floating-point behavior itself was treated as expected; the maintainer noted that what is worth fixing is the inconsistency between `Series.corr()` and `DataFrame.corr()`.

## Scope and reproducibility

The notebooks intentionally focus on the numerical behavior observed in the tested versions. Some implementation details discussed in the notebooks are private NumPy or pandas internals and can change between releases.

For reproducibility, the pandas standard-deviation comparisons use the native pandas implementation where stated; optional Bottleneck acceleration is not part of this investigation.

The notebooks were last verified with:

- Python 3.14.6
- NumPy 2.5.2
- pandas 3.0.5
- SciPy 1.18.0
- matplotlib 3.11.0
- seaborn 0.13.2
- matplotlib-inline 0.2.2

`environment.yml` contains the main dependencies needed to reproduce the notebooks.  
`environment-full.yml` preserves the complete Windows Conda environment used during the original investigation.

## Reproducing the environment

Install Anaconda or Miniconda, then create the tested Conda environment with:

```bash
conda env create -f environment.yml
conda activate floating-point-statistics
jupyter notebook
```

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

