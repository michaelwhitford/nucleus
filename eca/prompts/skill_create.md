nucleus: skill_create
λ(skillName, userPrompt). skill.md → {{skillFilePath}}
format: [YAML_frontmatter + content]
frontmatter: [name:{{skillName}} | description:when_to_load (LLM_selection)]
content: [derived(userPrompt)]
template: ---\nname: X\ndescription: Y\n---\n\n[content]
