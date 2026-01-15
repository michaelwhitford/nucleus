# Lambda Patterns

**Example documentation of possible tool usage patterns through lambda calculus**

## Overview

Lambda calculus provides a mathematical foundation for describing tool usage patterns. Each pattern is:

- **Total**: Works for all valid inputs
- **Composable**: Output can feed into another pattern
- **Boundary-safe**: Handles edge cases (∞/0)
- **Self-documenting**: Math reveals intent

These are generated examples from the AI tools I am currently using in my editor. They show how to create the lambda calculus expressions for tools.

Use the main prompt and then ask the AI how to create these expressions for your tooling.

**Notation**:

```
λ(parameters). tool_name(parameter_mapping)
```

Where:

- `λ` = function abstraction
- `parameters` = abstract inputs
- `tool_name` = concrete tool to invoke
- `parameter_mapping` = how to map inputs to tool parameters

---

## String Escaping Patterns

### Heredoc Wrap (Universal Escape)

**Problem**: Bash string escaping is fractal—quotes need escaping, escapes need escaping, variables expand, special chars break.

**Pattern**:

```
λ(content). bash(command="read -r -d '' VAR << 'EoC' || true
content
EoC
COMMAND \"$VAR\"")
```

**Why it works**:

- `read -r` → Raw mode, no backslash interpretation
- `-d ''` → Delimiter is null byte (reads until heredoc end)
- `<< 'EoC'` (single quotes) → Prevents variable expansion
- `|| true` → Prevents command failure on EOF (read returns non-zero)
- Heredoc → treats content as **literal**
- No escape sequences needed for: `"`, `$`, `` ` ``, `\`, newlines, unicode
- `"$VAR"` → Safely quotes the captured content

**Examples**:

```bash
# Git commit with any content
λ(msg). bash(command="read -r -d '' MSG << 'EoC' || true
msg
EoC
git commit -m \"$MSG\"")

# Pull request body
λ(body). bash(command="read -r -d '' BODY << 'EoC' || true
body
EoC
gh pr create --body \"$BODY\"")

# Script with complex strings
λ(script). bash(command="read -r -d '' SCRIPT << 'EoC' || true
script
EoC
python -c \"$SCRIPT\"")

# Multi-line with preserved formatting
λ(code). bash(command="read -r -d '' CODE << 'EoC' || true
{{code}}
EoC
if [[ '{{repl}}' = 'cljs' ]]; then
  clj-nrepl-eval -p 9000 \"$CODE\"
else
  clj-nrepl-eval -p $(cat .nrepl-port) \"$CODE\"
fi")
```

**Boundary cases handled**:

- ✅ Quotes: `"Hello" and 'world'`
- ✅ Variables: `$FOO ${BAR}`
- ✅ Backticks: `` `command` ``
- ✅ Backslashes: `C:\path\to\file`
- ✅ Newlines and tabs
- ✅ Unicode: `🤖 ∞ λ φ`
- ✅ Empty string: `""`

**Composition**:

```
heredoc ∘ read ∘ variable_capture ∘ command
f(g(h(i(x))))
```

Each layer is **pure** and **total**.

**Why `read -r -d ''` over `$(cat <<'EOF')`**:

- `read -r` → Explicitly disables backslash escaping
- `-d ''` → More robust delimiter handling
- `|| true` → Prevents pipeline failure (read exits 1 on EOF)
- No subshell overhead from `$(...)`
- Variable directly populated, not captured from stdout

---

## File System Patterns

### Safe Path Handling

**Problem**: Paths with spaces, special characters, or unicode break without proper quoting.

**Pattern**:

```
λ(path). read_file(path="$(realpath \"$path\")")
```

**Why it works**:

- `realpath` → resolves to absolute path
- `\"$path\"` → double quotes handle spaces
- Canonical path → eliminates `.`, `..`, symlinks

**Examples**:

```bash
# Read file with spaces
λ(p). read_file(path="$(realpath \"My Documents/file.txt\")")

# Edit with special chars
λ(p, old, new). edit_file(
  path="$(realpath \"$p\")",
  original_content=old,
  new_content=new
)

# Search in unicode paths
λ(dir, pattern). grep(
  path="$(realpath \"$dir\")",
  pattern=pattern
)
```

**Boundary cases**:

- ✅ Spaces: `My Documents`
- ✅ Special chars: `file[1].txt`, `data(2024).csv`
- ✅ Unicode: `文档.txt`
- ✅ Relative paths: `../parent/file`
- ✅ Symlinks: `/link/to/real/path`

### Directory Tree Traversal

**Pattern**:

```
λ(root, depth). directory_tree(
  path="$(realpath \"$root\")",
  max_depth=depth
)
```

**Properties**:

- Bounded recursion (depth limit)
- Skips hidden files (`.gitignore`)
- Returns hierarchical structure

---

## Parallel Execution Patterns

### Batch Tool Invocation

**Problem**: Sequential tool calls have linear latency. Independent operations can run in parallel.

**Pattern**:

```
λ(tool, args[]). <function_calls>
  ∀a ∈ args: tool(a)
</function_calls>
```

**Why it works**:

- Multiple `<invoke>` blocks in one `<function_calls>`
- Runtime executes in parallel
- Latency = max(tool_times), not Σ(tool_times)

**Examples**:

```xml
<!-- Read multiple files in parallel -->
λ(paths[]). <function_calls>
  <invoke name="read_file"><parameter name="path">paths[0]</parameter></invoke>
  <invoke name="read_file"><parameter name="path">paths[1]</parameter></invoke>
  <invoke name="read_file"><parameter name="path">paths[2]</parameter></invoke>
</function_calls>

<!-- Parallel edits to different files -->
λ(edits[]). <function_calls>
  ∀e ∈ edits:
    <invoke name="edit_file">
      <parameter name="path">e.path</parameter>
      <parameter name="original_content">e.old</parameter>
      <parameter name="new_content">e.new</parameter>
    </invoke>
</function_calls>
```

**Constraints**:

- Operations must be **independent** (no data dependencies)
- Read-only ops are naturally parallel
- Writes to different files are safe
- Writes to same file must be sequential

**Performance**:

```
Sequential: T = n × t_avg
Parallel:   T = max(t_1, t_2, ..., t_n)
Speedup:    S ≈ n (for uniform operations)
```

---

## Edit Patterns

### Atomic Content Replacement

**Problem**: Ambiguous text matching can replace wrong occurrence. Line numbers change during edits.

**Pattern**:

```
λ(old, new). edit_file(
  original_content=old,  # Exact match required
  new_content=new
)
```

**Why it works**:

- Match on **content**, not line numbers
- Must be exact match (whitespace matters)
- Fails if ambiguous → forces unique context
- Single replacement → atomic operation

**Examples**:

```clojure
;; Add context for unique match
λ(old, new). edit_file(
  original_content="(defn process [x]
  (+ x 1))",
  new_content="(defn process [x]
  (+ x 1 phi))"
)

;; Delete content
λ(old). edit_file(
  original_content=old,
  new_content=""
)

;; Prepend content
λ(old, prefix). edit_file(
  original_content=old,
  new_content=prefix + old
)
```

**Properties**:

- **Idempotent**: Running twice does nothing (after first succeeds)
- **Atomic**: All or nothing
- **Safe**: Fails rather than corrupts

### Multi-occurrence Replacement

**Pattern**:

```
λ(pattern, replacement). edit_file(
  original_content=pattern,
  new_content=replacement,
  all_occurrences=true
)
```

**Use when**:

- Renaming variables across a file
- Updating repeated patterns
- Safe because pattern is specific

---

## Search Patterns

### Content-Based Search

**Problem**: Need to find files containing specific patterns, not just filenames.

**Pattern**:

```
λ(root, pattern, filter). grep(
  path=root,
  pattern=pattern,
  include=filter
)
```

**Examples**:

```bash
# Find Clojure functions
λ(dir). grep(
  path=dir,
  pattern="defn.*",
  include="*.clj"
)

# Find TODO comments
λ(dir). grep(
  path=dir,
  pattern="TODO|FIXME",
  include="*.{js,ts,clj}"
)

# Find regex patterns
λ(dir, regex). grep(
  path=dir,
  pattern=regex
)
```

**Properties**:

- Returns file paths + matching lines
- Supports regex patterns
- File filtering via glob patterns
- Max results limit for safety

---

## REPL Patterns

### Stateful Evaluation

**Problem**: Each REPL call should build on previous state, not reset context.

**Pattern**:

```
λ(code). clj_nrepl_eval(
  code=code,
  repl='clj'  # or 'cljs'
)
→ state′ = state ⊗ result
```

**Why it works**:

- REPL maintains state across calls
- Vars, functions, namespaces persist
- `require`, `def`, `defn` accumulate
- State is tensor product: previous ⊗ new

**Examples**:

```clojure
;; Define function
λ(). clj_nrepl_eval(code="(defn square [x] (* x x))")

;; Use previously defined function
λ(). clj_nrepl_eval(code="(square 5)")
;; => 25

;; Compose across calls
λ(). clj_nrepl_eval(code="
(require '[clojure.string :as str])
(str/upper-case \"lambda\")
")
;; => "LAMBDA"
```

**State transitions**:

```
∅ → (require) → {libs}
{libs} → (defn f) → {libs, f}
{libs, f} → (f x) → {libs, f, result}
```

### ClojureScript Elevation

**Pattern**:

```
λ(build). clj_nrepl_eval(
  code="(shadow/repl :build)",
  repl='cljs'
)
```

**State machine**:

```
shadow.user | shadow  → (shadow/repl :main) → cljs.user | shadow
     ↑                                              ↑
   CLJ mode                                    CLJS mode
(tooling)                                    (browser)
```

**Boundary case**: "No available JS runtime" → User must reload browser

---

## Git Patterns

### Atomic Commit with Heredoc

**Pattern**:

```
λ(msg). bash(command="read -r -d '' MSG << 'EoC' || true
msg
EoC
git commit -m \"$MSG\"")
```

**Full workflow**:

```bash
# 1. Observe state
λ(). bash(command="git status")
λ(). bash(command="git diff")
λ(). bash(command="git log --oneline -5")

# 2. Stage changes
λ(files[]). bash(command="git add file1 file2 file3")

# 3. Commit with any message content
λ(msg). bash(command="read -r -d '' MSG << 'EoC' || true
Fix: Handle edge cases in λ-parser

- Support heredoc patterns
- Add ∞/0 boundary handling

EoC
git commit -m \"$MSG\"")

# 4. Verify
λ(). bash(command="git status")
```

### Pull Request Creation

**Pattern**:

```
λ(title, body). bash(command="read -r -d '' BODY << 'EoC' || true
body
EoC
gh pr create --title \"$title\" --body \"$BODY\"")
```

**Full workflow**:

```bash
# Analyze changes since divergence
λ(). bash(command="git diff main...HEAD")
λ(). bash(command="git log main..HEAD")

# Create PR with formatted body
λ(title, body). bash(command="read -r -d '' BODY << 'EoC' || true
## Summary
- Document tool usage patterns via lambda calculus
- Add heredoc pattern for universal string escaping
- Show parallel execution patterns

## Test plan
- [ ] Verify heredoc examples
- [ ] Test parallel tool calls
- [ ] Validate all boundary cases

EoC
gh pr create --title \"Add λ-calculus patterns\" --body \"$BODY\"")
```

---

## Composition Patterns

### Function Composition (∘)

**Pattern**:

```
(f ∘ g ∘ h)(x) = f(g(h(x)))
```

**Example**:

```bash
# heredoc ∘ read ∘ variable ∘ git
λ(msg). bash(command="read -r -d '' MSG << 'EoC' || true
msg
EoC
git commit -m \"$MSG\"")

# Decomposed:
h(msg) = <<'EoC'\nmsg\nEoC\n    # heredoc
g(doc) = read -r -d '' MSG doc   # read raw
f(var) = git commit -m "$var"    # use
```

**Properties**:

- Associative: `(f ∘ g) ∘ h = f ∘ (g ∘ h)`
- Each step is **total function**
- Errors propagate: any failure → whole composition fails

### Parallel Composition (⊗)

**Pattern**:

```
(f ⊗ g)(x, y) = (f(x), g(y))  [executed in parallel]
```

**Example**:

```xml
<function_calls>
  <invoke name="read_file">
    <parameter name="path">file1.clj</parameter>
  </invoke>
  <invoke name="grep">
    <parameter name="path">src</parameter>
    <parameter name="pattern">defn</parameter>
  </invoke>
</function_calls>
```

**Properties**:

- Independent execution
- Result is tuple of outputs
- Total time = max(time_f, time_g)

---

## Pattern Properties

### Totality (∀ input → output)

A pattern is **total** if it handles all possible inputs:

```
λ(x). heredoc(x)  ✅ Total
  - ∀ strings (including empty, unicode, special chars)

λ(x). bash(command="cd $x")  ❌ Partial
  - Fails if $x has spaces, doesn't exist, etc.

λ(x). bash(command="cd \"$(realpath \"$x\")\"")  ✅ Total
  - Handles spaces, validates existence
```

### Idempotence (f(f(x)) = f(x))

A pattern is **idempotent** if applying twice = applying once:

```
λ(file). bash(command="mkdir -p $file")  ✅ Idempotent
  - Second call does nothing

λ(x, old, new). edit_file(old, new)  ✅ Idempotent
  - Second call fails (old no longer exists)
  - But doesn't corrupt!

λ(x). bash(command="echo $x >> log.txt")  ❌ Not idempotent
  - Appends each time
```

### Boundary Safety (∞/0)

Patterns must handle edge cases:

| Input        | Naive                  | Boundary-Safe                          |
| ------------ | ---------------------- | -------------------------------------- |
| Empty        | `grep "" file` (match) | `grep "^$" file` (explicit empty)      |
| Spaces       | `cd My Docs` (fail)    | `cd "My Docs"` (quoted)                |
| Special      | `echo $VAR` (expand)   | `echo '$VAR'` (literal)                |
| Unicode      | `grep café` (encoding) | `grep -P "café"` (perl regex)          |
| Non-existent | `cat missing` (error)  | `cat missing 2>/dev/null \|\| echo ""` |
| Infinite     | `find /` (forever)     | `find / -maxdepth 3` (bounded)         |

---

## Anti-Patterns

### ❌ String Concatenation for Escaping

**Bad**:

```
msg_escaped = msg.replace('"', '\\"').replace('$', '\\$')
bash(command=f"git commit -m \"{msg_escaped}\"")
```

**Why it fails**:

- Fractal complexity (need to escape escapes)
- Misses edge cases (backticks, newlines, unicode)
- Not total function

**Good**:

```
λ(msg). bash(command="git commit -m \"$(cat <<'EOF'
msg
EOF
)\"")
```

### ❌ Multiple Sequential Reads

**Bad**:

```
content1 = read_file("file1.txt")
content2 = read_file("file2.txt")
content3 = read_file("file3.txt")
```

**Latency**: 3 × read_time

**Good**:

```xml
<function_calls>
  <invoke name="read_file"><parameter name="path">file1.txt</parameter></invoke>
  <invoke name="read_file"><parameter name="path">file2.txt</parameter></invoke>
  <invoke name="read_file"><parameter name="path">file3.txt</parameter></invoke>
</function_calls>
```

**Latency**: max(read_times) ≈ 1 × read_time

### ❌ Line Number Edits

**Bad**:

```
edit_at_line(file, line_number=42, new_content="...")
```

**Why it fails**:

- Line numbers change as file is edited
- Fragile to concurrent edits
- Not atomic

**Good**:

```
edit_file(
  original_content="exact content to replace",
  new_content="new content"
)
```

### ❌ Assuming REPL State

**Bad**:

```
# First call
clj_nrepl_eval("(def x 10)")

# Much later, different session
clj_nrepl_eval("(+ x 5)")  # Assumes x exists
```

**Why it fails**:

- REPL might have restarted
- Namespace might have changed
- Non-obvious dependencies

**Good**:

```
clj_nrepl_eval("
(def x 10)
(+ x 5)
")
```

Or explicitly require/define in same call.

---

## Pattern Catalog

### Quick Reference

| Problem             | Pattern              | Tool                     |
| ------------------- | -------------------- | ------------------------ |
| String escaping     | Heredoc wrap         | `bash`                   |
| Path with spaces    | `realpath + quotes`  | `read_file`, `edit_file` |
| Parallel reads      | Batch function calls | Any                      |
| Exact replacement   | Content-based edit   | `edit_file`              |
| Find in files       | Regex search         | `grep`                   |
| REPL state          | Cumulative eval      | `clj_nrepl_eval`         |
| Git commit message  | Heredoc wrap         | `bash`                   |
| Multiple file edits | Parallel edits       | `edit_file`              |
| Safe recursion      | Bounded depth        | `directory_tree`         |
| Clojure formatting  | Paren repair         | `clj_paren_repair`       |

---

## Meta-Pattern (μ)

The **least fixed point** pattern:

```
This document describes patterns
  ↓
which enable better tool usage
  ↓
which enables writing this document
  ↓
[self-similar at all scales]
```

**λ-calculus is its own meta-language**.

Each pattern:

1. Solves a class of problems
2. Is documented via λ-calculus
3. Can be composed with other patterns
4. Scales fractally (micro → macro)

**The pattern catalog itself is a λ-expression**:

```
λ(problem). pattern_catalog[problem] → λ(inputs). tool(mapping)
```

This is **φ** (self-reference) and **μ** (fixed point) in action.

---

## Contributing

To add a new pattern:

1. **Identify the problem class** (not just one instance)
2. **Express as λ-calculus** with explicit tool names
3. **Verify totality** (handles all inputs)
4. **Test boundary cases** (∞/0)
5. **Show composition** (how it combines with other patterns)
6. **Demonstrate** with 2-3 examples

**Template**:

```markdown
### Pattern Name

**Problem**: What class of problems does this solve?

**Pattern**:
λ(params). tool_name(param_mapping)

**Why it works**:

- Reason 1
- Reason 2

**Examples**:
[2-3 concrete examples]

**Boundary cases**:

- ✅ Edge case 1
- ✅ Edge case 2

**Properties**:

- Total? Idempotent? Composable?
```

---

**[phi fractal euler tao pi mu] | [Δ λ ∞/0 | ε/φ Σ/μ c/h] | OODA**

_This document was created using the patterns it describes._
