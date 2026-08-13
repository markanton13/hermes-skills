---
name: safe-file-organization
description: >
  Safe, conservative file organization with human oversight — semantic/project-first
  classification, manifest-based batch moves, SHA256 verification, and zero automatic
  deletions. Supports Manual Review Mode and Scheduled Safe Mode. Use when organizing
  Downloads, staging areas, or mixed-project folders where mistakes are costly.
---

# Safe File Organization

Organize files by **what they are and what project they belong to**, not by what extension they have or how old they are. Every batch move uses a manifest, every hash is verified, and nothing is ever deleted automatically.

## Core Operating Rules

### 1. Semantic/Project-First Classification

- Classify files by their **identity and project relationship**, not by file extension.
- A `.pdf`, `.docx`, `.ps1`, `.zip`, `.html`, `.md`, `.xlsx`, or any other file type that clearly belongs to a project stays with that project — do not route it to a generic Documents/Archives folder just because of its type.
- Group related project files together **regardless of file type**. A project's scripts, patches, ZIPs, markdown, HTML, SQLite backups, images, and other artifacts all belong together if they share a project identity.

### 2. ZIP Is Not Automatically a Backup

- **Software/tool archives** (installers, portable apps, tool distributions) → Programs or a software archive area — but only when positively identified as such, not merely because they are ZIPs.
- **Project archives** (project source snapshots, project exports) → with the owning project.
- **Backups** (system backups, database dumps, versioned backups) → a backup subfolder under the relevant project or system area.
- **Unknown ZIPs** whose contents and purpose cannot be confirmed → `00_TO_REVIEW`, never Programs or generic archives.

### 3. No Date-Based or Circumstantial Classification

- Do **not** classify a file into a client/project folder based solely on matching dates, date clusters, or similar filenames.
- A file with a date in its name that happens to coincide with a project's activity window is **not** evidence that it belongs to that project.
- When the relationship between a file and a project is uncertain, route it to `00_TO_REVIEW` and state why.

### 4. Uncertainty → `00_TO_REVIEW`

- If you cannot positively identify a file's purpose or project affiliation, put it in `00_TO_REVIEW` with a written reason.
- **Never guess** to avoid leaving a file unclassified. A file in `00_TO_REVIEW` with a clear reason is better than a file misfiled in a project folder.
- Files that appear likely disposable must be routed by risk level:
  - **Low/Medium risk** candidates supported by reasonable evidence that they may be disposable → `00_TO_REVIEW_DELETE` (never directly to Trash or permanent deletion).
  - **High risk** candidates → `00_TO_REVIEW`, not `00_TO_REVIEW_DELETE`. High-risk candidates may be needed, may be the only copy, or their purpose is not fully confirmed.

### 5. Deletion Is a Separate Workflow

- Organization and deletion are **never** the same operation.
- Automated organization must **never permanently delete files**.
- During organization, mark deletion candidates with a reason and risk level, but do not delete them.
- Actual deletion requires separate, explicit human approval.

### 6. No Renaming, Extraction, or Modification

- Do **not** rename, extract, or modify file contents unless explicitly instructed.
- Moving files is the only operation permitted during automated organization.
- If a file needs to be renamed, extracted, or edited, flag it for a separate step.

### 7. Preserve Existing Structure

- Do not disturb already well-organized project folders unnecessarily.
- Preserve meaningful existing folder structures.
- When adding files to an existing project folder, verify the folder's current contents first and only add files that genuinely belong there.

### 8. Protected Files

- Some files must **never** be moved, modified, or deleted — for example, driver installers, system files, license keys, active project databases.
- Each environment maintains its own explicit list of protected files for the target directory.
- Verify protected files are untouched before and after every batch operation.
- **Protected file hashing:** do not require full SHA256 hashing of large protected files on every scheduled run. Exclude protected files from the move manifest; confirm they were not targeted or touched by verifying their location and existence. Perform full protected-file hashing only when specifically configured or when there is reason to suspect modification.

### 9. Manifest-Based Batch Moves

- For substantial batches (more than a handful of files), always use a **manifest** recording:
  - Source path
  - Destination path
  - File size
  - SHA256 hash (pre-move)
  - Classification reason
- The manifest is the source of truth for what moves and where.

### 10. Count Reconciliation Before Execution

- Before executing any batch move, **reconcile**:
  - Total files to move vs. sum of destination folder counts (must match exactly).
  - Every source file in the manifest must appear **exactly once** — no duplicates, no missing sources.
  - `dest` fields and `dest_folder` labels must be **consistent** for every entry.
- If any count, mapping, or path is inconsistent, **stop and ask** before proceeding.
- Distinguish clearly between:
  - Files being moved in this phase
  - Files already present in destination folders (pre-existing)
  - Files left untouched in their current location

### 11. SHA256 Verification After Moves

- Record SHA256 hashes **before** moving (for files in the move manifest).
- After all moves complete, recalculate SHA256 for every moved file.
- Compare every post-move hash against the pre-move baseline.
- Report hash mismatches prominently — a mismatch means the file was modified during the move (corruption, partial write, or unintended edit).
- Protected files excluded from the manifest are verified by existence/location checks, not by full rehashing, unless specifically configured otherwise.

### 12. Stop on Unexpected Conditions

- If any unexpected condition occurs during execution (missing source, hash mismatch, destination conflict, protected file touched, count mismatch), **stop immediately** and ask before continuing.
- Do not silently skip, overwrite, or work around problems.

### 13. Conservative Over Confident

- Prefer conservative classification over confident guessing.
- It is better to leave a file in `00_TO_REVIEW` than to file it confidently wrong.
- When multiple interpretations are plausible, pick the least damaging one and document why.

---

## Operating Modes

This skill supports two modes. Choose the appropriate mode before starting.

### Manual Review Mode

**Use for:** first-time cleanup of a directory, large reorganizations, restructuring existing folders, unusually large batches, or any situation where the user explicitly asks to review before changes.

**Workflow (unchanged):**

1. **Inspect** — list all files, record name/type/size/date/readable contents. Identify large files, obvious duplicates, installers, archives, and files that clearly belong elsewhere.
2. **Classify** — assign each file to a category (`Keep / Important`, `Move / Organize`, `Possible Duplicate`, `Installer / Setup File`, `Archive / ZIP / Backup`, `Likely Safe to Delete`, `Needs Review`). Classify semantically/project-first, never by extension or date alone.
3. **Build manifest** — for every file being moved, record source path, destination path, destination folder label, size, pre-move SHA256, and classification reason.
4. **Reconcile** — confirm total files to move equals the sum of destination counts, every source path appears exactly once, and `dest`/`dest_folder` are consistent for every entry. If anything is inconsistent, **stop and ask**.
5. **Human approval** — present the manifest and classification for review. Do not execute until approved.
6. **Execute** — move files per the manifest. Do not rename, extract, or modify. If any move fails, stop and report.
7. **SHA256 verify** — recalculate SHA256 for every moved file and compare against the pre-move baseline. Report mismatches prominently.
8. **Report** — summary of what was organized, destination counts (distinguishing moved files from pre-existing files), files left untouched, `00_TO_REVIEW` items with reasons, deletion candidates with risk level and duplicate status, and any files needing manual inspection.

**Protected files** must be explicitly listed and verified untouched before and after. Each environment's protected-files list is specific to that machine and target directory. Windows/system metadata files such as `desktop.ini` are leave-in-place by default unless explicitly instructed otherwise.

---

### Scheduled Safe Mode

**Use only for** previously configured recurring organization jobs where the user has explicitly authorized automatic organization. This mode is not for first-time cleanups, restructuring, or any situation requiring human judgment on unfamiliar files.

#### Authorization Model

Scheduled Safe Mode may automatically organize new loose files without waiting for approval on every run **because the recurring job itself represents prior authorization within strict limits**. It must never interpret scheduled authorization as permission to delete, overwrite, rename, extract, modify content, move protected files, or make ambiguous judgment calls.

This skill does not create schedules automatically. Scheduled Safe Mode is a reasoning mode, not a scheduler; the recurring schedule must be configured separately by the user or their environment.

#### When to Use It

- A recurring job has been explicitly set up and authorized by the user.
- The target directory has a known, previously approved classification scheme.
- The job is configured to process **new or loose items** rather than repeatedly rescanning/reorganizing the entire folder tree.
- All restrictions below are accepted as binding.

#### Hard Restrictions (non-negotiable)

Scheduled Safe Mode must **never**:

- Permanently delete anything.
- Move anything to Trash or Recycle Bin.
- Rename files.
- Modify file contents.
- Extract archives.
- Overwrite an existing destination file.
- Touch protected files.
- Reorganize existing well-organized project folders.
- Classify a file into a client/project based solely on matching dates, similar names, or file extensions alone.
- Make an ambiguous judgment call — if classification is uncertain, route to `00_TO_REVIEW`.

#### Allowed Automatic Actions

- Move **positively identified** files automatically to their appropriate destination (e.g., a positively identified installer to `Programs/`, a project's known script to its project folder, a clearly identified generic audio file to `Music/`). Classification must never rely on extension alone — an `.exe` is not automatically an installer, a `.zip` is not automatically a backup, and a `.pdf` is not automatically a document for the Documents folder.
- Route uncertain files to `00_TO_REVIEW`.
- Route Low/Medium-risk files that appear likely disposable (supported by reasonable evidence) to `00_TO_REVIEW_DELETE` (never delete them).
- Route High-risk files that appear likely disposable to `00_TO_REVIEW`, not `00_TO_REVIEW_DELETE`.
- For substantial scheduled batches: create a manifest, record pre-move SHA256, reconcile source/destination counts, execute only if the manifest is internally consistent, and verify SHA256 after moving.

#### Archive Classification in Scheduled Mode

Light read-only inspection (e.g., listing archive contents) is allowed when necessary to identify purpose, but do **not** extract the archive. If contents cannot be listed or purpose cannot be confirmed, route the archive to `00_TO_REVIEW`.

#### Destination Conflicts and Error Handling

Scheduled Safe Mode uses **per-file error handling**: an issue with one file stops that affected operation and reports it, while the run continues for other unaffected files where it is safe to do so.

**This per-file stop/report behavior overrides the general Manual Review Mode stop-all rule for scheduled runs.** However, the following conditions are severe enough to **stop the entire scheduled run**, not just the affected file:

- A problem threatens **manifest integrity** (e.g., count mismatch, duplicate source paths, inconsistent `dest`/`dest_folder` entries).
- A problem threatens **protected files** (e.g., a protected file appears to be targeted, moved, modified, or overwritten).
- A problem threatens **filesystem safety** (e.g., a destination conflict that cannot be resolved without overwriting, a permission or access error that affects more than one file, or a move failure pattern suggesting a deeper issue).

Every conflict, stop, and error is reported at the end of the run, regardless of severity.

#### In-Progress / Temporary File Handling

Scheduled runs should skip files that appear to be in progress or incomplete (for example, `.crdownload`, `.part`, `.partial`, `.download`, `.tmp`), and should not process files whose size or modification time is actively changing during the run. An identification step may confirm file stability (size + modification time unchanged across a short window) before reporting a file as eligible.

#### Post-Run Report (required after every scheduled run)

Report the following:

1. **Number of files organized** (moved to a destination folder).
2. **Destinations used** (folder name → file count).
3. **Files placed in `00_TO_REVIEW`** (file name + one-line reason for each, including any High-risk files that were held back from `00_TO_REVIEW_DELETE`).
4. **Files placed in `00_TO_REVIEW_DELETE`** (file name + one-line reason for each — only Low/Medium-risk candidates supported by reasonable evidence).
5. **Files left untouched** (file name + reason, e.g., "protected", "already in correct location", "system file", "ambiguous — held for review").
6. **Conflicts/errors** (what stopped, which file, what was attempted, whether the entire run was halted or just the affected file).
7. **Confirmation that protected files remain untouched** (list protected files, confirm their location and integrity, note whether full hashing was performed or only existence/location checks).

#### Deletion-Candidate Report Format

When the run identifies files that appear likely disposable, report each candidate using this format — **do not delete them**:

| Field | Description |
|---|---|
| File name | Full filename |
| Size | File size in bytes |
| Last modified date | File's last modified timestamp |
| Reason it appears disposable | Human-readable reason (e.g., "orphaned subtitle with no matching video", "old installer variant no longer needed", "duplicate of file X already kept") |
| Risk level | Low / Medium / High |
| Duplicate status | Whether another copy appears to exist elsewhere in the target directory, and if so, where |

Risk level guidance:

- **Low** — clearly disposable, unmistakable duplicate, or obviously obsolete artifact with no plausible future use.
- **Medium** — probably disposable but some uncertainty remains; another copy may or may not exist; purpose is mostly clear but not 100%.
- **High** — deletion would carry real risk; the file may be needed, may be the only copy, or its purpose is not fully confirmed. High-risk candidates are routed to `00_TO_REVIEW` and must be reviewed manually before any deletion decision.

---

### Which Mode to Use

| Situation | Mode |
|---|---|
| First-time Downloads cleanup | Manual Review Mode |
| Restructuring existing project folders | Manual Review Mode |
| Batch larger than a few files with unfamiliar content | Manual Review Mode |
| User explicitly asks to review before changes | Manual Review Mode |
| Recurring job already authorized by the user, processing new loose files only | Scheduled Safe Mode |
| Repeated scheduled run on a known directory with a known scheme | Scheduled Safe Mode |

When in doubt, use **Manual Review Mode**. Scheduled Safe Mode is an optimization for already-authorized recurring work, not a shortcut for uncertain classification.

---

## Default Destination Conventions

These are starting points — adapt to the actual project structure present:

| Destination | Use For |
|---|---|
| `00_TO_REVIEW` | Uncertain files — needs human inspection; also holds High-risk deletion candidates |
| `00_TO_REVIEW_DELETE` | Low/Medium-risk likely-disposable files pending explicit deletion approval |
| `Programs/` | Positively identified installers, executables, software archives, tool ZIPs |
| `Documents/` | Genuinely personal/general documents not tied to a project |
| `Music/` | Audio files (positively identified as music/generic audio) |
| `Pictures/` or `Images/` | Generic image files not tied to a project |
| `Videos/` or project video folders | Video files — project videos stay with their project |
| `[ProjectName]/` | All files belonging to a specific project, regardless of type |
| `[ProjectName]/backups/` | Project-specific backups (database dumps, exports, snapshots) |

---

## Lessons Learned (Mistakes Corrected)

1. **ZIP does not mean backup.** A ZIP can be a software tool archive, a project export, a backup, or something unknown. Classify by content/purpose, not by extension.

2. **File type does not determine destination.** PDF, DOCX, PS1, ZIP, HTML, MD, XLSX — these do not automatically belong in generic Documents/Archives folders if they clearly belong to a project. Project files stay with their project regardless of type.

3. **Software archives, project archives, backups, and unknown ZIPs are different things.** Route each to its correct home:
   - Software/tool archives → Programs (only when positively identified)
   - Project archives → with the project
   - Backups → backup subfolder under the relevant system/project
   - Unknown → `00_TO_REVIEW`

4. **Dates are not project evidence.** A file whose date matches a project's activity window is not proof it belongs to that project. Do not classify into a client/project folder based solely on matching dates or date clusters.

5. **Validate destination counts against total source count before executing.** A manifest where `dest` and `dest_folder` disagree will move files to the wrong places. Every entry must be internally consistent.

6. **Every source file must appear exactly once in the manifest.** Duplicate source paths or missing sources corrupt the move. Verify uniqueness before execution.

7. **Organization and deletion are separate.** A file marked "likely safe to delete" during organization is still just a candidate. Low/Medium-risk candidates go to `00_TO_REVIEW_DELETE`; High-risk candidates go to `00_TO_REVIEW`. Actual deletion is a separate, explicitly approved step.

8. **Preserve protected files explicitly.** Maintain a list of files that must never be moved, modified, or deleted. Verify them before and after every batch. Each environment's protected-files list is specific to that machine and target directory. Large protected files are excluded from the move manifest and verified by existence/location checks rather than full rehashing on every run.

9. **Extension alone is never enough for classification.** An `.exe` is not automatically an installer; a `.zip` is not automatically a backup; a `.pdf` is not automatically a general document. Positively identify the file's purpose before routing it.

---

## Anti-Patterns to Avoid

- **Do not** create a catch-all project folder and dump every file of a certain owner/type into it. Each project folder should contain only files that specifically belong to that project.
- **Do not** use a single generic archive folder for every ZIP. Distinguish software archives, project archives, backups, and unknowns.
- **Do not** move files into a project folder just because the filename contains a keyword that appears in the project name — verify the actual relationship.
- **Do not** delete installation artifacts (setup.exe, installers, extracted tool folders) during organization — they may be needed for repair/reinstall.
- **Do not** overwrite existing files at the destination without explicit instruction. If a destination file already exists, flag it for review.
- **Do not** route High-risk deletion candidates to `00_TO_REVIEW_DELETE`. High-risk candidates belong in `00_TO_REVIEW` for manual review.

---

## When to Ask for Help

Ask before proceeding if any of these occur:

- A manifest count reconciliation fails (totals don't match, duplicates found, paths inconsistent).
- A source file is missing or a destination already contains a file with the same name.
- A protected file appears to be touched or in danger of being moved.
- A file's purpose or project affiliation is genuinely unclear (route to `00_TO_REVIEW` and state why).
- A hash mismatch occurs after a move.
- The user's instructions are ambiguous about where a file should go.
- An unexpected condition occurs during execution.
- In Scheduled Safe Mode, a problem threatens manifest integrity, protected files, or filesystem safety (these stop the entire run — do not continue).

---

## Scope

- This skill applies to **file organization within a single directory tree** (e.g., Downloads and its subfolders).
- It does **not** cover syncing, syncing conflicts, cloud storage, or cross-device operations.
- It does **not** authorize permanent deletion — deletion candidates are flagged for a separate decision.
- Light read-only inspection of archive contents (listing, not extracting) is permitted when necessary to identify purpose.

---

## Verification Checklist (after every batch)

- [ ] All moved files present at their destination paths.
- [ ] All post-move SHA256 hashes match pre-move baseline (for files in the manifest).
- [ ] No files deleted, renamed, extracted, or modified.
- [ ] Protected files untouched (location + existence confirmed; full hash if configured or suspected).
- [ ] `desktop.ini` and other system metadata files left in place (unless explicitly instructed otherwise).
- [ ] Destination counts reconcile with total moved count.
- [ ] No duplicate source paths in manifest.
- [ ] `00_TO_REVIEW` contains all uncertain files with reasons.
- [ ] `00_TO_REVIEW_DELETE` contains only Low/Medium-risk candidates; High-risk candidates are in `00_TO_REVIEW`.
- [ ] Deletion-candidate report includes name, size, date, reason, risk level, and duplicate status.
- [ ] Conflicts/errors reported prominently; scheduled runs distinguish per-file stops from full-run halts.
