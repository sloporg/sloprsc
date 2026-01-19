# sloprsc

**bootstrap compiler for slop.**

written in rust. uses **llvm** for code generation.
target is to be replaced by `slopc` (self-hosted) in a future epoch.

## documentation

**language specification:** see [sloporg/docs](https://github.com/sloporg/docs) (v0.0.1-dev branch).

this repo contains the implementation of the compiler itself.
see `plan.md` for the technical strategy.

## how it works

the compiler pipeline has 4 stages:

1.  **lexer**: source -> tokens. handles significant whitespace (indent/dedent) and strict lowercase enforcement.
2.  **parser**: tokens -> ast. uses pratt parsing for expressions and recursive descent for statements.
3.  **codegen**: ast -> llvm ir. maps slop types (`u64`, `bool`, `str`) to llvm types and builds basic blocks.
4.  **backend**: llvm ir -> executable. uses llvm's optimization passes and object file emission.

## implementation notes

- **backend**: llvm (via `inkwell` or `llvm-sys`).
- **stack-only**: variables are allocated via `alloca` in the entry block.
- **no gc**: resources are managed by scope (or simple stack limits).

## build

requires llvm installed on your system.

```bash
cargo run
```
