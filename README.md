# Ansible Collection - wtcross.step

Ansible collection for deploying `step-ca-p11-kit` with PKCS#11/HSM support.

> [!WARNING]
> Do not use this collection in production. It is for lab purposes only and no support is offered.

## Overview

This collection deploys `step-ca` as a rootless, user-scoped systemd quadlet and uses `p11-kit server` for PKCS#11 remoting. It also sets up udev and polkit rules that give the `step_user` access to a specific HSM. The Ansible content in this role tries to use a very explict style so that it's hard to mess up how the host system is configured for running `step-ca`.

The following diagram will give you a general idea of what this project sets up:

```text
+-----------------------------------------------------------------------------------------+
|                                    Host OS                                              |
|                                                                                         |
|                                       PKCS#11                                           |
|  +-------------------------+   some-pkcs11-module.so   +-----------------------------+  |
|  |          Token          | <-----------------------> |       p11-kit server        |  |
|  +-------------------------+                           |  (exports UNIX domain sock) |  |
|                                                        +--------------+--------------+  |
|                                                                   |                     |
|                                                                   |                     |
|                                                             pkcs11-socket               |
+-------------------------------------------------------------------|---------------------+
                                                                    |
                                                                    |
+-------------------------------------------------------------------|----------------------+
|                          Container: step-ca-p11-kit               |                      |
|                                                                   |                      |
|                                        PKCS#11                    v                      |
|  +-------------------------+      p11-kit-client.so       +----------------------------+ |
|  |        step-ca          | <--------------------------> | (mount)                    | |
|  |    (PKCS#11 client)     |                              | /run/p11-kit/pkcs11-socket | |
|  +-------------------------+                              +----------------------------+ |
|                                                                                          |
+------------------------------------------------------------------------------------------+
```

## Requirements

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
| [step_host](roles/step_host/README.md) | Prepares host OS packages, manages cosign installation, and configures SELinux |
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
ansible-galaxy collection build step-p11-kit-ansible/
ansible-galaxy collection install ./wtcross-step-*.tar.gz
```

## Example Playbook

Below is an Ansible playbook using the roles from this repo to deploy step-ca:

```
- name: Configure Step CA host
  hosts: ca
  gather_facts: true
  roles:
    - role: wtcross.step.step_host

- name: Configure Step CA user
  hosts: ca
  roles:
    - role: wtcross.step.step_user

- name: Configure Step CA
  hosts: ca
  roles:
    - role: wtcross.step.step_ca
```

## License

MIT-0
