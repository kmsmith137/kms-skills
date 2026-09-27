# Journal schema

Schema for `JOURNAL_<n>.md`, the toplevel agent's log of campaign *n* (see
`SKILL.md` for the rules; this file is only the format).

## Layout

    # Campaign <n>: <short title>

    <restatement: the question, what a good answer looks like, which code is
    relevant, which earlier conclusions this campaign builds on>

    - started: <UTC time> (<date +%s>); horizon: <e.g. 24 h>
    - stop: <absolute UTC time>

    ## Notes

    - (2026-08-03 14:10) <my steering message, a question and my answer, a
      horizon change with old and new stop times, ...>

    ## Research questions

    - RQ-A: <question> (raised by agent004)
    - RQ-B: ~~<question>~~ **settled by agent007**: <the answer, in one line>

    ## GPU assignments

    - GPU 0: agent009 (since 15:20)

    ## Experiments

    <one entry per subagent, oldest first>

    ## Closing

    <what was learned, what is still open, what you would do next>

Settled research questions may stay, struck through, while their answers are
useful; the list must still empty out as the campaign converges.

## Entry

    ### agentNNN -- <one-line title>

    - **date**: YYYY-MM-DD
    - **status**: done | failed | superseded
    - **question**: the one thing this experiment was meant to settle.
    - **setup**: parameters, code used, seed. Enough to re-run.
    - **result**: the headline numbers, and wall-clock time. "Inconclusive" if
      it was.
    - **verdict**: what this changes about the plan: pursue, drop, or follow up,
      and why.
    - **files**: `agentNNN/REPORT.md`, plus anything notable (plots, data,
      scripts worth reusing).
    - **epilogue**: added later, as often as needed.

- **status**: `failed` = crashed, ran out of time, or wrote no report.
  `superseded` is set later, when a result is overturned; leave the original
  text alone and explain in the epilogue.
- **result**: numbers, not adjectives. If the result is a curve or frontier,
  put it in the report and quote one representative point here.
- **epilogue**: the only part of an entry that changes after it is written.
  Each addition is dated and names the agent that caused it: a result
  superseded or refuted, an error found later, a claim narrowed to a regime.

## Example

Illustrative only; the domain will differ.

    ### agent007 -- SVD of dense A at rank 10, no detrender

    - **date**: 2026-08-03
    - **status**: superseded
    - **question**: is a truncated SVD ever admissible under D?
    - **setup**: rank-10 tree, 400 channels, no detrender, seed 42; dense A,
      SVD via numpy; CPU only.
    - **result**: 64 singular values reach D = 0.03; rank 32 gives D = 0.21.
      Building dense A took 6 min.
    - **verdict**: the no-detrender case is easy; this is the baseline to
      compare against once a detrender is on. Next: rerun with W = 0 and W = 4.
    - **files**: `agent007/REPORT.md`, `agent007/spectrum.png`
    - **epilogue**:
      - (2026-08-05, agent014) Superseded: these numbers come from a synthetic
        matrix; on real maps, rank 64 gives D = 0.21, not rank 32. The
        qualitative conclusion survives; don't quote these numbers.
