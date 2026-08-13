# Hermes Skills

This repository contains reusable skills for **Hermes Agent**.

Skills are self-contained instruction sets that extend Hermes with domain-specific behavior, safety rules, and operating procedures. Each skill lives in its own folder under `skills/` and is referenced by name when invoked in a Hermes prompt.

---

## Available Skills

| Skill Name | Description | Version | Link |
|---|---|---|---|
| `safe-file-organization` | Safe, conservative file organization using semantic/project-first classification, review staging, manifest-based moves, SHA256 verification, and zero automatic deletions. | `v1.0.0-beta` | [skills/safe-file-organization/](skills/safe-file-organization/) |

To add another skill, create a new folder under `skills/{skill-name}/`, add its `SKILL.md`, and add a row to this table.

---

## How to Use

1. Browse the **Available Skills** table above.
2. Open the skill's folder (for example, `skills/safe-file-organization/`).
3. Read that skill's `README.md` for human-readable documentation.
4. Install or reference the skill's `SKILL.md` in your Hermes profile's skills directory. In a Hermes prompt, this looks conceptually like:

   ```
   skills/{skill-name}/SKILL.md
   ```

5. If needed, start a fresh Hermes session so the skill is loaded into the agent's context.
6. Invoke the skill by name in a prompt.

### Install from GitHub

To install a skill from this repository into your local Hermes setup:

```
hermes skills install markanton13/hermes-skills/skills/safe-file-organization
```

This requires Hermes to be able to access the public GitHub repository. If the install command needs a different path format, adapt the repository + path accordingly.

As a direct raw-file alternative (if supported by your Hermes version):

```
hermes skills install @url:`https://raw.githubusercontent.com/markanton13/hermes-skills/main/skills/safe-file-organization/SKILL.md`
```

After installing, verify with:

```
hermes skills list
```

Installed skills normally become available in a fresh Hermes session. If the skill does not appear, start a new session.

The important pattern is that each skill is a folder containing `SKILL.md` (and optionally a `README.md`).

---

## License

Licensed under the MIT License. See [LICENSE](LICENSE).
