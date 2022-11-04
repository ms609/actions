# GitHub actions for the R language

A collection of GitHub actions I've developed to enact continuous integration
on my [R packages](https://smithlabdurham.github.io/#!software).

These build on [r-lib actions](https://www.tidyverse.org/blog/2022/06/actions-2-0-0/)
to facilitate some common tasks.

## List of actions

1. [ms609/actions/codemeta.R](https://github.com/ms609/actions/tree/main/codemeta):
  Write package metadata to `codemeta.js`
1. [ms609/actions/pkgdown.R](https://github.com/ms609/actions/tree/main/pkgdown):
  Create package documentation website with [pkgdown](https://pkgdown.r-lib.org/).
  Adapted from [r-lib example](https://github.com/r-lib/actions/tree/v2/examples#build-pkgdown-site).
1. [ms609/actions/revdepcheck.R](https://github.com/ms609/actions/tree/main/revdepcheck):
 Check reverse dependencies using [r-lib/revdepcheck](https://github.com/r-lib/revdepcheck).
1. [ms609/actions/validate-ctv.R](https://github.com/ms609/actions/tree/main/validate-ctv):
  Ensure that CRAN task views are valid.
