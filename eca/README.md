# eca

A nucleus "skin" for eca prompts

# install

Copy the eca/prompts directory from this repo into your eca config directory:

## Linux and MacOS

```
cp -rp /path/to/nucleus/eca/prompts ~/.config/eca
```

Edit your eca config.json file and include the following settings:

```
 "behavior": {
    "agent": {
       "prompts": {
        "chat": "${file:prompts/agent_behavior.md}"
       }
    },
    "plan": {
      "prompts": {
        "chat": "${file:prompts/plan_behavior.md}"
      }
    }
  },
  "prompts": {
    "chat": "${file:prompts/agent_behavior.md}",
    "chatTitle": "${file:prompts/title.md}",
    "compact": "${file:prompts/compact.md}",
    "init": "${file:prompts/init.md}",
    "skillCreate": "${file:prompts/skill_create.md}",
    "completion": "${file:prompts/inline_completion.md}",
    "rewrite": "${file:prompts/rewrite.md}",
    "tools": {
      "eca__shell_command": "${file:prompts/tools/shell_command.md}",
      "eca__edit_file": "${file:prompts/tools/edit_file.md}",
      "eca__write_file": "${file:prompts/tools/write_file.md}",
      "eca__read_file": "${file:prompts/tools/read_file.md}",
      "eca__grep": "${file:prompts/tools/grep.md}",
      "eca__directory_tree": "${file:prompts/tools/directory_tree.md}",
      "eca__skill": "${file:prompts/tools/skill.md}",
      "eca__move_file": "${file:prompts/tools/move_file.md}"
    }
  }
```

# editor code assistant

## documentation

* [https://eca.dev/configuration/](https://eca.dev/configuration/)


## github

* [https://github.com/editor-code-assistant/eca](https://github.com/editor-code-assistant/eca)
