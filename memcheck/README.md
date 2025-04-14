# Check for memory errors using valgrind

This GitHub action checks C/C++ code within a package for memory errors using
`valgrind`.

# Using this workflow

<!--1. Add a line to your package's `DESCRIPTION` file reading

`Config/Needs/website: pkgdown`

Add any other packages required to build the website, separating by commas.
-->
1. Create a file `.github/actions/memcheck.yml` with the content:

```yml
on:
  push:
    branches:
      - main
      - master
      - '**valgrind**'
    paths:
      - '.github/workflows/memcheck.yml'
      - 'src/**'
      - 'inst/include/**'
      - 'memcheck/**'
      - 'tests/testthat/**.R'
      - 'vignettes/**.Rmd'
  pull_request:
    branches:
      - main
      - master
    paths:
      - '.github/workflows/memcheck.yml'
      - 'src/**'
      - 'inst/include/**'
      - 'memcheck/**'
      - 'tests/testthat/**.R'
      - 'vignettes/**.Rmd'

name: mem-check

jobs:
  mem-check:
    runs-on: ubuntu-24.04

    name: valgrind ${{ matrix.config.test }}

    strategy:
      fail-fast: false
      matrix:
        config:
          - {test: 'tests'}
          - {test: 'examples'}
          - {test: 'vignettes'}

    env:
      R_REMOTES_NO_ERRORS_FROM_WARNINGS: true
      _R_CHECK_FORCE_SUGGESTS_: false
      RSPM: https://packagemanager.rstudio.com/cran/__linux__/noble/latest
      GITHUB_PAT: ${{ secrets.GITHUB_TOKEN }}

    steps:
      - uses: ms609/actions/memcheck@main
        with:          
          test: ${{ matrix.config.test}}
```

For simplicity, removing the `strategy:` and `with:` blocks will test the three
aspects of a package in serial.
