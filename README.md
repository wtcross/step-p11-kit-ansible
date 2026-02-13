# Ansible Collection - wtcross.step

Ansible collection for deploying `step-ca-p11-kit` with PKCS#11/HSM support on RHEL.

> [!WARNING]
> Do not use this collection in production. It is for lab purposes only and no support is offered.

## Overview

This collection deploys `step-ca` as a rootless, user-scoped systemd quadlet and uses `p11-kit server` for PKCS#11 remoting.

## Requirements

- Container host must be on `RHEL 10.1` or later
- User configured as `ansible_user` must be able to become `root`
- Controller running the playbook must be able to reach GitHub raw content

## Collection Dependencies

This collection requires:

- `ansible.posix` >= 1.0.0
- `containers.podman` >= 1.0.0

Install dependencies:

```bash
ansible-galaxy collection install ansible.posix containers.podman
```

## Roles

| Role | Description |
| --- | --- |
| [step_host](roles/step_host/README.md) | Prepares the host OS with required packages |
| [step_user](roles/step_user/README.md) | Creates dedicated user and configures HSM access |
| [step_ca](roles/step_ca/README.md) | Deploys `step-ca-p11-kit` via rootless quadlets using upstream unit/script assets |

## Role Documentation

Role-specific variables, behavior, and examples are documented in each role README:

- [roles/step_host/README.md](roles/step_host/README.md)
- [roles/step_user/README.md](roles/step_user/README.md)
- [roles/step_ca/README.md](roles/step_ca/README.md)

## Installation

Install from Galaxy:

```bash
ansible-galaxy collection install wtcross.step
```

Or install from source:

```bash
ansible-galaxy collection build step-ansible/
ansible-galaxy collection install ./wtcross-step-*.tar.gz
```

## License

MIT-0
