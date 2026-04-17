# Contributing to ViperScope

Thanks for your interest in contributing! This project welcomes bug reports, improvements to the detection algorithm, and new false-positive tests.

---

## Ground rules

All contributions must maintain **NASA Power of Ten compliance**. Before submitting a pull request, check that your code:

- Has no recursion
- Has explicit upper bounds on every loop
- Pre-allocates arrays — no appending inside hot loops
- Has functions ≤ 60 lines
- Has two assertions per function (entry + exit)
- Checks every return value before use; `None` returns are explicit
- Adds constants to the config block, not inline as magic numbers
- Never mutates input arrays in-place (use `.copy()`)

If you're unsure about any of these, open an issue and describe what you want to do — it's easier to discuss before writing the code.

---

## Reporting bugs

Open an issue and include:

- Python version and OS
- The full error traceback
- The target name or TIC ID that triggered the bug (if applicable)
- The contents of `viperscope_survey/progress.json` if the survey runner was involved

---

## Suggesting improvements

Good areas for contribution:

- Additional false-positive tests (e.g. centroid shift, difference imaging)
- Support for Kepler / K2 light curves
- Multi-sector stitching
- Period recovery improvements (BLS, TLS)
- Performance optimisation for large batches

Open an issue first to discuss the approach before writing code.

---

## Pull request process

1. Fork the repository and create a branch from `main`
2. Make your changes, following the coding rules above
3. Test on at least one known planet (WASP-17b is the reference target in Section 3)
4. Update the README if your change affects user-facing behaviour
5. Open a pull request with a clear description of what changed and why

---

## Code style

- PEP 8 formatting
- Type hints on all function signatures
- Docstrings in NumPy format
- No magic numbers — use the config block constants

---

## Questions

Open a GitHub Discussion or an issue tagged `question`.
