# deadcanary

**Find the data tests that cannot fail.**

> Stars, issues and the GitHub Action live here. **Development, the test suite, and every
> release happen in [Cshearer210/claimproof](https://github.com/Cshearer210/claimproof), at
> [`packages/deadcanary`](https://github.com/Cshearer210/claimproof/tree/main/packages/deadcanary)**
> — deadcanary and claimproof are one idea at two layers, joined in code: deadcanary
> contributes a claimproof `Gate`, and the proof it produces expires on its own when the
> test suite changes. `pip install deadcanary` installs this package either way.

A canary that is already dead cannot warn you about anything, and it looks exactly like
one that is alive and well.

Data test suites fill up with them. A team accumulates hundreds of `not_null`, `unique`
and `accepted_values` checks over a couple of years. Every one of them is green every
morning. Some are green because the data is healthy. Some are green because they were
never capable of going red — the column they watch is behind a join that drops the bad
rows, or the model rebuilds from a source the test never sees, or the assertion is simply
about something that cannot happen.

Nobody can tell those two groups apart by looking, and nobody ever checks.

The only way to know is to **break the data on purpose and see which tests notice.**

![A dbt project is built, its data is corrupted on purpose one column at a time, and the tests that never noticed are named. Two of seven green tests turn out to be incapable of failing.](https://raw.githubusercontent.com/Cshearer210/claimproof/main/packages/deadcanary/assets/demo.svg)

*A live run of `python -m deadcanary.demo`, drawn from the demo's real output — never
pre-recorded, regenerated from a live run every time it changes.*

This is **mutation testing** — decades old, well proven for source code
([`mutmut`](https://pypi.org/project/mutmut/), [`cosmic-ray`](https://pypi.org/project/cosmic-ray/))
— pointed at data quality rules instead of at functions.

## What it found in dbt-labs' own template

**6 of 20 green tests in dbt-labs' current jaffle-shop template cannot fail.** Among them
`unique_orders_order_id` and `not_null_orders_order_id` — the two most common tests in
dbt. 102 corruptions were applied to that project and **76 caught nothing at all.**

Two more real projects measured, one with zero dead canaries and a lesson in what that
does and does not prove: [FINDINGS.md](FINDINGS.md).

## Try it in one minute

```bash
pip install deadcanary[demo]
python -m deadcanary.demo
```

It builds a small warehouse, corrupts it on purpose, and re-runs the suite against each
corruption — every line is a real dbt run, nothing pre-recorded. Already have a dbt
project? `pip install deadcanary[dbt]` then `python -m deadcanary path/to/project`.

## Use it in CI

```bash
deadcanary path/to/dbt/project --baseline .deadcanary-baseline.json --update-baseline
```

Fails the build only if the dead-canary count goes UP from what's recorded — a healthy,
growing test suite never breaks it, only a real regression does. Or as a GitHub Action:

```yaml
- uses: Cshearer210/claimproof/packages/deadcanary@main
  with:
    project: path/to/dbt/project
```

Full CLI reference, the five ways a tool like this can lie to you and what stops each
one, and what it deliberately does not claim: the
[full README](https://github.com/Cshearer210/claimproof/tree/main/packages/deadcanary#readme)
at the source.

## Licence

MIT.
