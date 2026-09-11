# Install quarto for r-hub checks

If an R package requires quarto, this GitHub Action detects a runner's
environment and installs quarto appropriately.

quarto use is detected by searching for the line `VignettesBuilder: quarto`
in the package's `DESCRIPTION` file.  The script assumes that
`uses: r-hub/actions/checkout@v1` has already been called.

It then pre-installs the package so that it is available for building vignettes.

The action is intended to be called from `rhub.yaml`.


# Using this workflow

