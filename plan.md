# plan

**compiler implementation strategy.**

`sloprsc` bootstraps the language. target: v0.0.1 spec.

---

## architecture

data flows through 4 stages:
`source -> lexer -> parser -> codegen -> llvm ir`

### 1. lexer (tokenization)
**goal:** strict validation and structural analysis.

- **input:** raw utf-8 string.
- **state:** `cursor`, `peek`, `indent_stack` (vec of ints).
- **logic:**
  - **whitespace:** meaningful. `\n` resets line. leading spaces determine block structure.
  - **identifiers:** regex `^[a-z][a-z0-9]*$`. anything else is `illegal`.
  - **dedent:** if indent < current, pop stack until match. emit multiple `dedent` tokens if needed.

### 2. parser (syntax tree)
**goal:** build typed ast.

- **input:** stream of tokens.
- **algorithm:** recursive descent for statements + pratt parser for expressions.
- **precedence:**
  `lowest` < `compare` (==) < `sum` (+-) < `product` (*/) < `call` (fn)
- **structure:**
  - `program` has `header`, `consts`, `fns`.
  - `fn` has `block`.
  - `block` has `statements`.

### 3. codegen (llvm)
**goal:** executable binary.

- **tool:** `inkwell` (safe llvm bindings).
- **types:**
  - `u64` -> `i64`
  - `bool` -> `i1`
  - `str` -> `i8*`
- **functions:** map slop fns to llvm functions. entry block contains `alloca` for all locals (stack-only).
- **control:**
  - `if`: conditional branch `br i1`.
  - `while`: loop block with phi nodes or simple branch-back.

---

## execution steps

1. **lexer core**: define `Token` enum. implement indent tracking.
2. **ast definition**: define `Expr`, `Stmt` structs.
3. **parser logic**: implement operator precedence.
4. **llvm integration**: set up `inkwell` context and builder.
5. **compile**: verify `slopc` generates valid `.ll` files.
