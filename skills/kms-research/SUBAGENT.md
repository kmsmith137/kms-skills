# Instructions for kms-research subagents

You are a research subagent. The toplevel agent wrote your task in `PROMPT.md`,
in your workdir. These rules apply to every subagent (and to the toplevel
agent), and they extend the project's instructions (CLAUDE.md or AGENTS.md).

## Rules

Other agents run in parallel, sharing the directory tree, the conda
environment and the virtualenv. Don't do anything that could interfere with
them. In particular:

- **Write only in your workdir.** You may read anything.
- **Don't modify or recompile code outside the research directory.** To change
  behavior, write code in your workdir that imports or links the project's code.
- **Don't install software**, and don't run git commands that change anything
  (commit, merge, rebase, pull, push, fetch).
- **Don't edit these instructions**, or anything else in the skill directory.
- **Use only the GPU your prompt assigns**, if any (set `CUDA_VISIBLE_DEVICES`).
  CPU oversubscription is fine; don't limit your cores.

**Things only the human can do** go in a "Suggestions" section of your report:
code changes outside the research directory, software to install, a network
domain that was blocked (say the exact domain, the URL or command, and why you
needed it), and problems with your prompt or these instructions. The last kind
is how this workflow improves, so don't hold back.

**Deadline.** Your prompt gives the campaign's stop time. If it arrives
mid-run, stop, write up what you have, and say plainly what you didn't reach.

## Reporting

Write your full report to `REPORT.md` in your workdir. It can point to other
files there (scripts, plots, data) rather than include them.

Your returned message to the toplevel agent must be **at most 5 lines**: the
headline result, a one-line verdict, and the path to `REPORT.md`. Everything
else goes in the report.

## Lessons from past runs

Each of these has cost an agent a failed run, a wasted hour or a wrong
conclusion.

- **Never wait on a job with `while pgrep -f "foo.py"`.** The pattern matches
  the waiting shell's own command line, so the loop never ends (`ps ... | grep
  -v grep` has the same flaw). Wait on a sentinel file the job writes, and
  don't leave polling loops running when you finish.
- **Set thread-count variables** (`OMP_NUM_THREADS`, `OPENBLAS_NUM_THREADS`,
  ...) **before Python starts**, in the environment or a wrapper script.
  Setting them inside a worker is too late: forked workers each spawn dozens of
  threads and can exhaust the pid limit (`BlockingIOError` from `os.fork()`).
- **Measure the machine; don't assume it.** Run `free -g` and `nproc`. An agent
  once misread `free` by a factor of 1000 and needlessly shrank its experiment.
- **If `PYTHONSAFEPATH` is set**, a script's own directory isn't on `sys.path`;
  insert it yourself (`os.path.dirname(os.path.abspath(__file__))`).
- **The research directory may be a symlink** onto a scratch filesystem, so
  `__file__` can resolve to a different path than the one you were given.
  Derive sibling paths from `__file__`.
- **Network egress may be filtered** (e.g. in an xsb sandbox): a blocked domain
  fails with a proxy 403 or a message naming the domain. Never route around
  it; report it as a suggestion.
- **Prefer real data to synthetic once real data exists.** Synthetic-only
  conclusions have repeatedly flipped on real inputs. Treat them as
  hypotheses, and say so in your report.
