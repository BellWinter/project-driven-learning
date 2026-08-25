# Contributing to Project Driven Learning

Thanks for considering a contribution! This skill improves through real usage — the best contributions come from people who actually used it and hit friction.

## Ways to contribute

### 1. Usage feedback (most valuable)
Use the skill to learn something, then open an issue describing:
- What stage/step felt awkward or unclear
- What the AI did wrong or missed
- What template was hard to fill or useless

This is how the skill gets better — it was iterated exactly this way.

### 2. Improve templates
The `assets/` templates are field-agnostic by design. If you find a template missing a field that mattered for your domain, propose the change.

### 3. Add a case study
Completed a project with this skill? Archive it as a case study:

- Format your troubleshooting records with `assets/troubleshooting-log-template.md`
- Add the archive as `references/<your-project>-case.md` (see existing cases for structure)
- Include: project overview, stage-by-stage implementation, real problem records, learning check examples, and a resume entry example

### 4. Cross-platform documentation
If you verified the skill works on a tool not listed in the README (or found the instructions inaccurate), update the installation table.

## Guidelines

- Keep everything in Markdown, no platform-specific syntax
- Keep `SKILL.md` lean — move details to `references/` or `assets/`
- Keep templates field-agnostic — no hardcoded tech stacks
- Update `CHANGELOG.md` with your change

## Process

1. Fork the repo
2. Create a branch: `git checkout -b feat/your-change`
3. Commit with a clear message
4. Open a Pull Request describing what and why

Small, focused PRs are preferred over large rewrites.

## License

By contributing, you agree your contributions are licensed under the [MIT License](LICENSE).
