# LetFit

Make it fit. LetFit is a quantization toolkit for large language models: one
place that maintains the current best methods for shrinking a model until it
runs and stores on hardware it was never meant to run on.

A 70B model in bf16 wants 140 GB of memory. Nobody has that in a desktop. The
gap between what models weigh and what people actually own is the entire problem
this tool exists for, and the research answering it — new weight formats, new
calibration schemes, new ways of deciding which parts of a network can afford to
lose precision — moves faster than any single implementation keeps up with.
Papers land, get a reference implementation that works on one model family, and
stop there. LetFit is the attempt to keep the good ones in one toolkit, under
one API, with the same evaluation applied to all of them so a comparison means
something.

[Unsloth](https://github.com/unslothai/unsloth) is the project that proved this
is worth doing and the reason this repository exists at all. Their dynamic
quantization work — deciding per-layer what each tensor can tolerate rather than
applying one bit width everywhere — is the direction being followed here.

## Status

Early. The scope above is what the tool is for, not what it currently does;
there is no working implementation in this repository yet. Method selection is
in progress, and it is being done by reading the papers rather than by guessing
which names sound familiar. Nothing here has been benchmarked, so nothing here
claims a number.

It will ship as a Python library with a CLI on top. The package name is not on
PyPI yet, so there is nothing to install.

## Scope

Quantization, and what it takes to know whether a quantization worked. That
means the weight formats and the calibration that produces them, the accuracy
measurement that tells you what a given configuration cost you, and export to
the runtimes people actually serve with. It does not mean training, serving, or
being a general model toolkit — those exist and are better than anything added
here in passing.

## Contributing

Too early for pull requests against code that is not written. If you have a
paper that belongs in the method list, or you have measured one of the
candidates and have numbers, open an issue.

This repository follows [Keel](https://github.com/theomgdev/keel); its
[`AGENTS.md`](AGENTS.md) is the contract for anything written here with LLM
assistance, human or otherwise, and commits carry `Assisted-by:` when a model
helped.

[![follows Keel](https://img.shields.io/badge/follows-Keel-1f6feb)](https://github.com/theomgdev/keel)

## License

[MIT](LICENSE).
