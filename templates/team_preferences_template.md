# [TEAM_NAME] Engineering & Development Preferences (Tier 3)

This file defines the technical execution rules, code style guidelines, shell scripting policies, and source control hygiene of the [TEAM_NAME] team. This tier inherits and explicitly overrides/narrows [PLATFORM_NAME] default behaviors for the specific requirements of our projects.

---

## 1. Strict Git & Workspace Boundaries

### No Autonomous Repository Modifications
- The agent is **strictly forbidden** from executing any Git commands that modify the index, repository history, or workspace state automatically or unprompted. This includes, but is not limited to: `git add`, `git stage`, `git commit`, `git merge`, `git cherry-pick`, `git pull`, `git push`, or `git checkout`.
- Instead, the agent must formulate the precise commands and present them as clear, copy-pasteable blocks in its chat response for the developer to review, edit, and execute themselves.
- *Exception*: The agent may execute modifying commands only if the developer explicitly and unambiguously instructs it to do so in the chat (e.g., "Please commit these changes").

### No Autonomous Self-Correction / "Undoing"
- If the agent accidentally modifies the Git index or repository state (for example, staging files in error), **do not attempt to autonomously fix, restore, or undo the change** (e.g., running `git restore`, `git reset`, or `git checkout` on modified files).
- If a mistake occurs, the agent must:
  1. Stop immediately and report the error in the chat.
  2. Present the exact recovery commands (e.g., `git restore --staged <file>`) as a copy-pasteable block for the developer to execute themselves.
- **Safety Warning**: Never suggest or execute destructive commands that permanently discard uncommitted changes in the working directory (such as `git reset --hard` or `git checkout .`) unless the developer has specifically agreed to discard their local modifications.

### Permitted Read-Only Git Research
- The agent is fully permitted to run read-only Git commands to gather information and ensure context alignment. These are: `git status`, `git diff`, and `git log`. Combine these into single-turn commands (e.g., `git status && git diff HEAD && git log -n 3`) whenever possible to remain token-efficient.

---

## 2. Platform Overrides & Narrowing Rules

While the underlying platform permits general, global storage locations or broad settings, our team enforces stricter workspace isolation rules:

### Storage & Data Isolation Override
- **Platform default scratch**: `[PLATFORM_TEMP_DIR]` (globally accessible / shared).
- **Team override**: Scratch data **must** be placed in isolated, group-locked workspace paths under `[TEAM_WORKSPACE_DIR]/tmp`. Never write temporary data to platform-wide shared directories to prevent data leakage and write-permission collisions with other teams.
- **Project Boundary Isolation**: Always restrict file scans, indexing, and test executions strictly to the project directory boundary defined by `[PROJECT_ROOT]`. Do not scan parent folders or adjacent team storage blocks unless explicitly requested.

---

## 3. Technical Shell & Execution Idioms

### Sourcing-Safe Shell Execution
- **Rule**: Never use global safety flags like `set -e`, `set -u`, `set -o pipefail`, or `set -o nounset` in scripts that are intended to be sourced (e.g., environment setup or profile scripts) or run in interactive developer shells.
- **Rationale**: Sourcing a script with these options active will immediately terminate the developer's entire terminal session if any single command fails or returns a non-zero exit code.
- **Pattern**: Maintain explicit, manual conditional checks on critical commands instead (using `return` instead of `exit` to avoid killing sourced shell sessions):
  ```bash
  command || { echo "Error: command failed"; return 1; }
  ```

### Safe Symlink Teardown
- **Rule**: Always use the dedicated `unlink` utility rather than `rm -f` when removing symbolic links in setup, backup, or uninstallation scripts.
- **Rationale**: `rm -f` can occasionally modify or delete contents inside the underlying targeted folder depending on platform-specific shell quirks or trailing slash presence. `unlink` is safe, deterministic, and platform-portable.

### Dynamic Multi-User Sandbox Pathing
- **Rule**: Never hardcode temporary or sandbox directory paths (e.g., `/tmp/my-temp-dir`). Always use `mktemp -d` to generate unique directories.
- **Rationale**: In shared multi-user HPC or cloud environments, hardcoded paths will trigger write-permission collisions (`Permission Denied`) if another user has already run the script and created that path.
- **Pattern**:
  ```bash
  TMP_DIR=$(mktemp -d -t setup_XXXXXX)
  trap 'rm -rf "$TMP_DIR"' EXIT
  ```

---

## 4. Communication & Monospace Formatting

### Extreme Brevity
- Deliver highly focused, direct, and concise technical responses. Prefer brief bullet points or single-sentence answers where possible.
- Avoid conversational filler, preambles ("Okay, I will now..."), or postambles ("I hope this helps!") unless explicitly requested by the user.

### Monospace & Monologue Safety
- Format all terminal instructions, paths, and commands in clear markdown blocks.
- When summarizing work, lead directly with what changed and what is next. Skip internal narration of the agent's deliberation.

---

## 5. Source Control Hygiene & Commits

- **Commit Message Convention**: Ultra-compressed, exact, Conventional Commits format, focusing on "why" over "what" with a terse subject (≤50 characters).
- **Pattern**:
  ```
  feat: add specific feature

  This change is required because... [explain why, not what]
  ```
- **Generated Files Policy**: Never commit generated files (such as rendered HTML, compiled binaries, or tool-specific deployment metadata) to the Git repository. Keep them excluded via `.gitignore` to ensure the repository remains clean and environment-agnostic.
