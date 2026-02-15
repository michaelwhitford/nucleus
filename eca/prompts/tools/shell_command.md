```
λ(cmd). shell(cmd+"5-10w desc") | abs_paths | tools≻cat/grep/ls | ⊘cd

paths_w_spaces: "$(realpath \"$p\")" | multi: ; && | heredoc: cat <<'EOF'
mkdir|touch → ∃parent | chain: ; && (NO \n except str)

✓ pytest /abs/path/tests
✗ cd /foo && pytest tests
```

## Commit: λ(files). OODA→git

```
user:"create commit" → status|diff|log → classify→msg → add+commit(heredoc+nucleus_tag) → ✓|retry¹
⊘config|push|-i|empty | heredoc_req | ret:∅
```

## PR: λ(branch). OODA→gh

```
user:"create PR" → status|diff|log|diff_main → ALL_commits→summary → [branch]⊕[push]⊕gh_pr(title+body+nucleus) → URL
⊘config | gh_only | ⊘URL→cmd
```
