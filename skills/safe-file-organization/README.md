# Safe File Organization

**Version:** `v1.0.0-beta`

Safe, conservative file organization using semantic/project-first classification, review staging, manifest-based moves, SHA256 verification, and zero automatic deletions. Supports **Manual Review Mode** and **Scheduled Safe Mode**.

This skill is for Hermes Agent. It encodes the reasoning and safety constraints an agent should follow when organizing files. It does not by itself execute moves, schedule runs, or delete files — that is left to the agent and the user's authorization.

---

## What It Does

The `safe-file-organization` skill helps an agent organize files into appropriate destinations without deleting, renaming, extracting, or modifying anything. It classifies files by what they are and what project they belong to — not by file extension or date alone — and uses a manifest to batch moves with pre- and post-move SHA256 verification.

It is designed for environments where mistakes are costly: Downloads folders, staging areas, mixed-project directories, and any folder containing a mix of installers, archives, project files, documents, and unknown items.

---

## Core Feature Summary

- **Manual Review Mode** — inspect, classify, build a manifest, and wait for human approval before executing any moves.
- **Scheduled Safe Mode** — authorized recurring organization that processes only eligible loose files, with strict safety limits and no automatic deletion.
- **Semantic/project-first classification** — files are classified by identity and project relationship, not by extension or date.
- **`00_TO_REVIEW`** — uncertain files are routed here for human inspection; also holds High-risk deletion candidates.
- **`00_TO_REVIEW_DELETE`** — Low/Medium-risk likely-disposable files are staged here pending explicit deletion approval; nothing is deleted automatically.
- **Manifest-based moves** — every batch move records source path, destination path, size, pre-move SHA256, and classification reason.
- **SHA256 pre/post verification** — hashes are recorded before moving and verified after; mismatches are reported prominently.
- **Protected-file handling** — each environment maintains its own protected-files list; protected files must never be moved, modified, or deleted.
- **No automatic deletion, overwrite, rename, extraction, or content modification** — moving is the only operation permitted during organization.

---

## How to Use

### Option 1 — Install the skill locally

Place the skill in your Hermes profile's skills directory. Conceptually:

```
{Hermes-profile}/skills/safe-file-organization/SKILL.md
```

The exact Hermes profile location may differ by OS and setup. For example, on one common configuration the profile lives under a user's application data directory; on another it may be in a project-local `.hermes/` folder. consult your Hermes installation documentation for the exact path.

Once the skill folder exists in the right place, Hermes can load it by name when you reference it in a prompt.

### Option 2 — Install from GitHub

The skill is published at:

```
skills/safe-file-organization/SKILL.md
```

To install it from GitHub into your local Hermes setup:

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

If your Hermes setup uses a different installation mechanism (for example, a config file or a manual copy), adapt accordingly. The key idea is the same: the skill is the `SKILL.md` file inside the `skills/safe-file-organization/` folder.

### Option 3 — Copy manually

Copy the `SKILL.md` file into your own Hermes profile's `skills/safe-file-organization/` folder. You can also copy the entire `skills/safe-file-organization/` directory if you want the accompanying `README.md` locally.

---

## Example Prompts

### Manual Review Mode

```
Use the safe-file-organization skill in Manual Review Mode to audit and organize {target-folder}. Do not make changes until you show me the proposed manifest and I approve it.
```

### Scheduled Safe Mode

```
Use the safe-file-organization skill in Scheduled Safe Mode for this already-authorized recurring folder organization job. Process only eligible loose files, never delete anything, route uncertain files to 00_TO_REVIEW, route supported Low/Medium-risk deletion candidates to 00_TO_REVIEW_DELETE, and report the results.
```

Replace `{target-folder}` with the actual directory you want organized. For a first run, start with a small or familiar folder so you can verify the classification behavior before trusting it on a larger set.

---

## Per-Machine Setup

Before first use, customize the following for your environment:

- **Target directory** — the folder you want organized (for example, a Downloads folder or a staging directory).
- **Protected files** — the files that must never be moved, modified, or deleted. Each machine has its own protected-files list.
- **Approved destination folders/conventions** — the destination folder names and project structures that already exist and are approved for use.
- **Mode** — whether Manual Review Mode or Scheduled Safe Mode is appropriate for the task.

Machine-specific paths, protected files, and schedules are intentionally **not** hardcoded into this public skill. They belong to each user's environment and must be configured per machine.

---

## Recommended First Run

If you are new to this skill, start with **Manual Review Mode** on an audit-only or approval-gated first run. Review the classification and proposed manifest before any moves are executed. Once you are confident the skill is classifying correctly for your folder and your protected-files list is correct, you can consider enabling Scheduled Safe Mode for an authorized recurring job.

---

## Important Safety Note

- **The skill never authorizes permanent deletion.** Deletion candidates are flagged with a reason and risk level for a separate, explicitly approved human decision.
- **`00_TO_REVIEW_DELETE` is staging only.** Files placed there are not deleted automatically; they await explicit deletion approval.
- **Scheduled Safe Mode should only be enabled after the user understands and approves the folder structure and classification behavior.** Scheduled authorization is not a shortcut for uncertain classification.

---

## License

Licensed under the MIT License. See [LICENSE](../../LICENSE).
