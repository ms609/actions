# Update codemeta information

This GitHub action updates package metadata using the [codemeta framework](
  https://codemeta.github.io/), producing and updating a file `codemeta.js`
  in an R package's source directory.

# Using this workflow

Create a `codemeta.yml` file in `user/PackageName/.github/actions/`,
containing:

```yml
on:
  push:
    branches:
      - main
      - master
    paths:
      - 'DESCRIPTION'
      - '**codemeta.yml'

name: codemeta

jobs:
  codemeta:
    runs-on: ubuntu-latest
    env:
      GITHUB_PAT: ${{ secrets.GITHUB_TOKEN }}

    steps:
      - uses: ms609/actions/codemeta@main
```

Replace `<TaskViewName>` with the name of the task view.
The task view should be contained in a file named `TaskViewName.md` in the
`user/TaskViewName` github repository.
