# General (language-agnostic)

### Documentation, comments, and logs
- Be concise; do not omit important points.
- Do not create or update docs (README, USAGE, etc.) unless requested.
- Write comments, log messages, and in-code text only when requested.

### Build/run and change recap
- Do not build, run, or verify by compiling unless asked.
- After code changes, summarize briefly what changed and where.

### Parameter / argument wrapping
- Applies to method/function **declarations** and **calls** (C#, TypeScript/TSX, similar languages).
- 1–3 parameters: keep on one line.
- More than 3: first parameter on the same line as the name; each following parameter on its own indented line; closing paren after the last parameter.

```text
// 1–3 — single line
Foo(a, b, c);

// 4+ — first param on first line
Foo(a,
    b,
    c,
    d);
```
