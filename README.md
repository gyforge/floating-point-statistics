# From Unexpected Correlation to Floating-Point Arithmetic in NumPy and pandas

This project began with an unexpected result while implementing lagged Pearson
correlation: constant input vectors could produce finite correlation values,
including values close to +1 or -1, even though Pearson correlation is
mathematically undefined for zero-variance inputs.

Understanding that result led to a deeper investigation of floating-point
mean-centering and standard deviation. Parts I and II document that branch,
including differences between NumPy and pandas and the role of floating-point
precision and reduction algorithms.

The following notebooks return to the original correlation problem and examine
how tiny numerical residuals can produce apparently strong positive or negative
correlations.

## Notebooks

1. [Part I — Floating-point arithmetic and rounding error](notebooks/01_Floating_point_arithmetic_rounding_error_Part-I.ipynb)  
   Reproduces non-zero standard deviations for constant arrays and compares NumPy with pandas.

2. [Part II — Why pandas returns zero for float32 constant arrays](notebooks/02_Floating_point_arithmetic_rounding_error_Part-II.ipynb)  
   Explains why the native pandas 3.0.5 path returns zero for constant float32 arrays by tracing its float64 accumulation. Optional Bottleneck acceleration is not covered.

## Environment

The notebooks were last verified with:

- python 3.14.6 
- numpy 2.5.2
- pandas 3.0.5
- matplotlib 3.11.0
- seaborn 0.13.2
- matplotlib-inline 0.2.2

environment.yml contains the main dependencies needed to reproduce the notebooks. 
environment-full.yml preserves the complete Windows Conda environment used during the original investigation.

## Reproducing the environment

Download and install Anaconda or Miniconda distribution

Create the tested Conda environment with:

```bash
conda env create -f environment.yml
conda activate floating-point-statistics
jupyter notebook