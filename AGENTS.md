# AGENTS.md

## Purpose and Scope

This file defines contribution standards for the `step-p11-kit-ansible` repository.

- Scope: applies to all files and directories in this repository unless a deeper `AGENTS.md` overrides with stricter rules.
- Goal: keep Ansible content predictable, reviewable, and consistent across roles.

## Repository Layout and Documentation

- Root `README.md` must stay high-level only (project purpose, requirements, installation, role list, links).
- Role-specific details must live in role READMEs:
  - `roles/step_host/README.md`
  - `roles/step_user/README.md`
  - `roles/step_ca/README.md`
- Do not add detailed variable tables or deep role implementation details to root `README.md`.
- Keep role README variable tables aligned with actual role inputs (`defaults/main.yaml`, preflight checks, and task usage).
- Reuse the same example values across role READMEs where values overlap.

## Ansible File Naming

- Use `.yaml` for all YAML files by default, including Ansible content files.
- Do not introduce new `.yml` files unless the filename is mandated by Ansible/Galaxy conventions (for example, `meta/runtime.yml`, `galaxy.yml`).

## Variable Naming Conventions

- External role variables must never be underscore-prefixed.
- Internal role variables that can be overridden by an external variable must use a single underscore prefix: `_var`.
- Internal role variables that are only computed from other variables must use a double underscore prefix: `__var`.

## Task and Handler Style

The following ordering is a general preference for readability.

- If a field is not used in a task or handler, do not add it just to satisfy ordering.
- If a field is used, keep this order:
  1. `name`
  2. `when`
  3. `become`
  4. `become_user`
  5. `delegate_to`
  6. `no_log`
  7. `environment`
  8. fully qualified module key (for example `ansible.builtin.fail`)
  9. `register`
  10. `ignore_errors`
  11. `changed_when`
  12. `loop`
  13. `notify`

- Always set `become` explicitly for tasks and handlers (`true` or `false`) so execution context is unambiguous, except for when using builtins like `include_tasks` that do not allow `become` to be set.
- Use fully qualified module names in tasks and handlers.

## Module Argument Ordering

Inside module blocks, use this general ordering when fields exist:

1. State or command fields (for example `state`, `enabled`, `cmd`, `argv`)
2. Identifying fields (for example `name`)
3. Timing fields (for example `immediate`)
4. Persistence fields (for example `permanent`)
5. Location fields (for example `path`, `dest`, `src`, `chdir`)
6. Ownership/access fields (for example `owner`, `group`, `mode`)
7. Other fields

## File Ownership and Permission Clarity

For modules that create/modify file content or file state (for example `file`, `copy`, `template`, `get_url`, `assemble`, `lineinfile`, `blockinfile`), explicitly set:

- `owner`
- `group`
- `mode`

Exception:
- For operations where these fields are not applicable in practice (for example some pure read-only inspection tasks), do not force unsupported arguments.

## Idempotency and Safety

- Prefer idempotent modules over shell/command when possible.
- When using command-like modules, set `changed_when` and/or `failed_when` when default behavior is ambiguous.
- Use `no_log: true` for tasks that handle secrets or sensitive values.
- Keep changes scoped and update docs when role inputs or behavior change.

## Python Dependency Management

This project uses uv for dependency management.

- To sync dependencies run `uv sync`
- The venv managed by uv is at `<project root>/.venv` and can be activated with `source .venv/bin/activate`
- Never update versions in uv.lock directly
- Always ask for approval before installing any packages or changing anything about uv configuration
