# clj-nrepl-eval

```
λ(code,repl). eval(code@nREPL) | code:clj_expr | repl:clj|cljs | auto_discover | persistent_session | auto_repair_parens
```

## Workflow

```
discover → [select_port] → eval(code) → result
persistent_session | auto_paren_fix | timeout:120s
```

## Usage Patterns

```
# Discovery (first use or new project)
discover_ports → select_server → eval

# Regular eval (session persists)
eval(expr) | state_persists | vars+ns+libs

# Session management
reset_session → eval | fresh_state
```

## Key Properties

- **Session Persistence**: Each host:port maintains state (vars, namespaces, libs) across invocations
- **Auto Repair**: Automatically fixes delimiter errors (missing/extra parens/brackets/braces)
- **Auto Discovery**: Scans .nrepl-port files and running JVM/Babashka processes
- **Stateful**: `(def x 10)` persists until server restart or `--reset-session`

## Port Selection

- `repl:clj` → reads `.nrepl-port` in current directory (default)
- `repl:cljs` → connects to port 9000 (shadow-cljs default)
- To discover available REPLs: use `shell_command` with `clj-nrepl-eval --discover-ports`

## Common Workflows

```
1. New REPL → discover_ports → eval
2. Known port → eval (session persists)
3. Reset needed → reset_session → eval
4. ClojureScript → specify repl:cljs
```

## Examples

```clj
;; Define state (persists)
(def x 10)
(defn square [n] (* n n))

;; Use state (same session)
(square x) ; => 100

;; Multi-expression
(do
  (require '[clojure.string :as str])
  (str/upper-case "hello"))
```

## Notes

- Returns evaluation result or error
- Supports heredoc/pipe for multi-line code
- Default timeout: 120000ms (2 minutes)
- Works with both Clojure (clj) and ClojureScript (cljs) REPLs
