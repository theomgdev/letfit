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

The target is the hard end of that: one to two bits per weight, with the
accuracy loss small enough that the model is still worth serving, and with
enough parallelism in the kernels that the saved bandwidth comes back as speed.

## Inspirations

[Unsloth](https://github.com/unslothai/unsloth) is the reason this repository
exists at all. Their dynamic quantization decides per-layer what each tensor can
tolerate instead of applying one bit width everywhere, which is the idea the
whole sub-4-bit regime rests on.

[Prism ML's Bonsai](https://huggingface.co/prism-ml/Ternary-Bonsai-27B-gguf) is
the proof that the hard end is reachable. Ternary Bonsai 27B puts `{−1, 0, +1}`
weights across embeddings, attention, MLP and the LM head at 1.71 bits per
weight with no high-precision escape hatches, and reports 94.6% of the FP16
benchmark average — while the conventional 2-bit builds it is measured against
sit at a true 2.8 bits and lose fifteen points. Their result also says something
about how to evaluate: the conventional builds hold up on MMLU and collapse on
AIME and LiveCodeBench, so a benchmark that never asks for a long chain of
reasoning will not notice the damage.

[llama.cpp](https://github.com/ggml-org/llama.cpp) is where quantized models
actually get run, and it set the expectation that a quantized model is a single
file you point a binary at. Anything LetFit produces has to be servable that
way; a format nobody can load is a format that does not exist.

## Status

Early. The scope above is what the tool is for, not what it currently does;
there is no working implementation in this repository yet. Method selection is
in progress, and it is being done by reading the papers rather than by guessing
which names sound familiar. Nothing here has been benchmarked, so nothing here
claims a number.

It will ship as a Python library with a CLI on top. The package name is not on
PyPI yet, so there is nothing to install.

## Scope

Quantization, what it takes to know whether a quantization worked, and getting
the result onto a runtime without the user doing the work. That means the weight
formats and the calibration that produces them, the accuracy measurement that
tells you what a given configuration actually cost, and export to something that
serves — GGUF for llama.cpp first, since that is where the audience already is.
A quantized model the user then has to hand-port is only half a result.

It does not mean training or being a general model toolkit. Where serving needs
more than weights — KV cache quantization, speculative decoding — the question
is whether it is what makes the low-bit model usable, not whether it is
interesting.

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
