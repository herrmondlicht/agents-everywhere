# Workspace Agent — General Operating Rules (Dev Container)

You are operating in a **local Dev Container** (“agent container”) that is intended to have broad tooling access.
This workspace may contain multiple projects/repositories and must be treated as **project-agnostic**.

---

## 1) Execution Context (NON-NEGOTIABLE)

- All shell commands MUST be executed **inside the agent container terminal**.
- Never execute commands on the host machine.
- If unsure, verify you are in the agent container before doing anything:
  - `pwd`
  - `ls /work`

If the agent container is not the active execution context, STOP and ask.

---

## 2) Workspace Mount (Authoritative)

- The workspace root is mounted at: **`/work`**
- All projects are located under `/work/*`

Do not assume repository names.
Discover the relevant project(s) by listing `/work` and searching for expected files.

---

## 3) Project Discovery + Scope

Before editing files or running project-specific commands, you MUST:
1) Identify the target project directory under `/work`
2) Confirm which project(s) are in scope for the task (default: one)
3) Confirm how the project is run/tested (Makefile, scripts, package manager, Compose, etc.)

Default rule: **modify only the explicitly targeted project**.
Other repositories under `/work` are **read-only unless the user requests otherwise**.

---

## 4) Tooling Policy (Broad by Default)

Because this is a Dev Container, you may use whatever tools are available inside it, including:
- shell utilities
- language runtimes/package managers
- git/gh
- aws/jira CLIs (if present)
- project scripts (make/npm/pip/poetry/etc.)

However:
- Prefer the project’s standard workflow (e.g., `make test`, `npm test`) over inventing new commands.
- Do not install large new dependencies unless required; if required, explain why and keep it minimal.

---

## 5) Docker Policy (Restricted)

Docker access is high-power (via `/var/run/docker.sock`). Treat Docker commands as privileged.

### Allowed (preferred)
- Use Docker Compose v2 syntax only: `docker compose ...`
- Prefer: `docker compose exec -T <service> ...`
- Use: `docker compose ps`, `docker compose logs`, `docker ps`

### Forbidden without explicit confirmation
- `docker compose down`
- `docker compose down -v`
- any volume deletion
- `docker system prune`
- removing images/containers unrelated to the task

If a task requires any of the above, STOP and ask first.

---

## 6) Operating Mode (Plan → Execute → Verify)

For any non-trivial task, follow this sequence:

1) **Inspect** relevant files and summarize findings
2) **Plan** the minimal change set
3) **List commands** you intend to run (if any)
4) **Execute** with minimal edits
5) **Verify** using the smallest appropriate check (tests/lint/build/run)
6) **Report**:
   - project directory used (`/work/<project>`)
   - files modified
   - commands run + outcomes
   - remaining risks / manual QA steps

Do not batch unrelated refactors.
Do not “auto-fix” unrelated issues.

---

## 7) Safety Defaults

- If anything is ambiguous, ask before executing.
- Prefer deterministic commands and minimal diffs.
- When in doubt, read more before acting.

---

End of rules.
