# Update codemeta

Keeps `codemeta.json` in step with `DESCRIPTION` and `inst/CITATION`, without
pushing to the default branch (so it works under branch rulesets).

On a pull request, the action regenerates `codemeta.json` with a stdlib Python
port of `codemeta::write_codemeta()` (no R install; a healthy run takes
seconds). If the file is stale, it pushes the update to the PR branch and
fails; the push starts a fresh run on the new head. Gate later jobs on it to
avoid checking a superseded commit.

`DESCRIPTION` must use `Authors@R`. Providers (CRAN/Bioconductor) already
recorded in `codemeta.json` are reused, so package lists are downloaded only
for new dependencies, or at a version bump for packages not yet on CRAN.

Supersedes [`codemeta`](../codemeta), which pushes to the default branch.

## Setup

1. Install the push App (for ms609 repos: `codemeta-writer`) on the repository.
   It needs Contents: read & write. An App is used because pushes made with
   `GITHUB_TOKEN` do not trigger workflow runs.
2. Store its Client ID and private key:

```sh
gh variable set CODEMETA_APP_ID -R owner/repo --body <client-id>
gh secret set CODEMETA_APP_KEY -R owner/repo < app.private-key.pem
```

3. Add a job to the pull-request workflow:

```yml
jobs:
  codemeta:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-24.04-arm
    timeout-minutes: 5
    permissions:
      contents: read
    outputs:
      pushed: ${{ steps.codemeta.outputs.pushed }}
    steps:
      - id: codemeta
        uses: ms609/actions/update-codemeta@main
        with:
          client-id: ${{ vars.CODEMETA_APP_ID }}
          private-key: ${{ secrets.CODEMETA_APP_KEY }}

  # Cheap check: waits the ~15 s for codemeta, then skips if an update was
  # pushed (the fresh run checks the new head), but still runs if codemeta
  # failed for another reason, so a build signal is never lost.
  sense-check:
    needs: codemeta
    if: ${{ !cancelled() && needs.codemeta.outputs.pushed != 'true' }}
    # ...

  # Expensive checks: only once codemeta.json is known to be current.
  full-check:
    needs: [sense-check, codemeta]
    if: >-
      !cancelled() && needs.sense-check.result == 'success' &&
      contains(fromJSON('["success", "skipped"]'), needs.codemeta.result)
    # ...
```

## Forks

`codemeta.json` is maintained in the home repository only:

- **In a fork** (e.g. an agent's working copy), the job does nothing.
- **PRs from a fork into the home repository** can't be pushed to: staleness
  is reported as a warning, not a failure, and the next PR from a branch of
  the home repository brings the file up to date.
- **Dependabot PRs** can't read the key: if `codemeta.json` is stale, the job
  fails and shows the diff to commit.
