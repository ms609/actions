# Build documentation with pkgdown

This GitHub action generates documentation for an R package using `pkgdown`.

# Using this workflow

1. Add a line to your package's `DESCRIPTION` file reading

`Config/Needs/website: pkgdown`

Add any other packages required to build the website, separating by commas.

2. Create a file `.github/actions/pkgdown.yml` with the content:

```yml
on:
  push:
    branches:
      - main
      - master
    paths:
      - 'DESCRIPTION'
      - '**pkgdown.yml'
      - '*.md'
      - 'inst/CITATION'
      - 'inst/*.bib'
      - 'man/**.Rd'
      - 'vignettes/**.Rmd'
  release:
    types: [published]
  workflow_dispatch:

name: pkgdown

jobs:
  pkgdown:
    runs-on: ubuntu-latest

    env:
      GITHUB_PAT: ${{ secrets.GITHUB_TOKEN }}

    steps:
      - uses: ms609/actions/pkgdown@main
```

Optionally add the commands `with: user-email`, `user-name` to attribute the
resulting commit to a GitHub user.

```yml
        with:
          user-email: actions@github.com
          user-name: GitHub Actions
```
