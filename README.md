# Notes to self

Notes, hints and observations I pick up during my coding time.

Some things I have tested, other things I have just picked up and noted here.

# pandas

## Faster numerical expressions in pandas series and dataframes

Pandas can make use of [`bottleneck`](https://github.com/pydata/bottleneck) and [`numexpr`](https://github.com/pydata/numexpr) to speed up computations.

When installed, pandas will use them automatically. Explicit configuration whether to use either via pandas options `compute.use_bottleneck` and `compute_use_numexpr` (both defaulting to True).
