# ngolshan/quiet-tizen — working notes

Fork of [jss8649/quiet-tizen](https://github.com/jss8649/quiet-tizen), used for two things at once:
running QuietTizen day to day on Nathan's Samsung S95C, and sending fixes back upstream.
Read the upstream `README.md` and `AGENTS.md` too; their safety rules apply here.

## Branches

| Branch | What it is | Rules |
|---|---|---|
| `main` | Mirror of `upstream/main` | Never commit to it. Update with `git fetch upstream && git switch main && git merge --ff-only upstream/main && git push origin main`. |
| `daily` | What actually runs on the TV: upstream + pieces taken from other forks + our own changes + this file | The only branch that gets built and installed on the TV. Merge `main` into it after each upstream sync. |
| `pr/<topic>` | One branch per upstream contribution | Always cut from `main`, never from `daily`. Bring commits in with `git cherry-pick` from `daily`. Push to `origin` and open the PR against `jss8649/quiet-tizen:main`. |

Keep commits on `daily` small and single-purpose, so any of them can be cherry-picked onto
a `pr/` branch cleanly. Commits that only make sense for this TV or this fork (this file,
local tweaks) stay on `daily` and never go into a PR.

Work taken from other forks: add the fork as a remote and cherry-pick or merge its branch into `daily`,
keeping the original author on the commits. If we later improve that work and want it upstream,
the PR should credit the original branch or build on its open PR rather than re-submitting it as ours.

## Remotes

- `origin`: `ngolshan/quiet-tizen` (push here)
- `upstream`: `jss8649/quiet-tizen` (fetch only; push is set to `NO_PUSH`)
- `dpearson`: `dpearson2699/quiet-tizen` (fetch only), which has the S95C profile on `feat/s95c-launcher-profile`

Add other forks the same way: `git remote add <name> <url> && git remote set-url --push <name> NO_PUSH`.

## The TV

- Model `QN65S95CAFXZA` (65" S95C, 2023), firmware `T-PTMCAKUC-0090-2220.4`.
- The IP address and anything else device-specific live in `.local/tv.env`, which is gitignored. Never commit
  IPs, serials, MACs, tokens, session logs, snapshots or pulled Samsung DLLs; they all go under `.local/`.
  Check `git diff --cached` for them before every push, because this fork is public.

## Line endings

This clone has `core.autocrlf=false`. Scripts run on the TV under `sh` and break with CRLF.
Don't re-clone with Windows defaults; if you do, set `git config core.autocrlf false` and re-checkout.
