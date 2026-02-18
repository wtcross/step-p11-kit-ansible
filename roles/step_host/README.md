# step_host

Prepares the host OS with required packages, managed cosign installation, and SELinux policy for step-ca deployment.

## Requirements

- User configured as `ansible_user` must be able to become `root`
- Ansible controller must have a trusted `cosign` on `PATH`
- Ansible controller must be able to reach GitHub API and release download endpoints

## Behavior

- Installs required host packages for step-ca deployment.
- Ensures `/usr/local/bin/cosign` exists and is updated to latest `sigstore/cosign` release
  when missing or older than latest release.
- Downloads and verifies cosign artifacts on the Ansible controller with identity-based
  verification using trusted controller `cosign`, then installs the verified binary on hosts.
- Installs the custom step-ca SELinux policy module from `step_ca_policy.cil`.

## Role Variables

| Variable | Required | Description | Default |
| --- | --- | --- | --- |
| `step_host_os_packages` | no | List of packages to install | acl, firewalld, python3-firewall, pcsc-lite, opensc, p11-kit, p11-kit-server, gnutls-utils, udica |
| `step_host_update_packages` | no | Whether to run dnf update | true |
| `step_host_reboot_if_required` | no | Whether to reboot if needed | true |
| `step_host_cosign_install_path` | no | Managed-host install path for `cosign` | `/usr/local/bin/cosign` |
| `step_host_cosign_host_architecture` | no | Required managed-host architecture for configured release asset | `x86_64` |
| `step_host_cosign_github_owner` | no | GitHub owner for cosign release lookup | `sigstore` |
| `step_host_cosign_github_repo` | no | GitHub repository for cosign release lookup | `cosign` |
| `step_host_cosign_release_asset` | no | Release asset name downloaded and installed | `cosign-linux-amd64` |
| `step_host_cosign_identity` | no | Expected signing certificate identity for release verification | `keyless@projectsigstore.iam.gserviceaccount.com` |
| `step_host_cosign_oidc_issuer` | no | Expected OIDC issuer for release verification | `https://accounts.google.com` |
| `step_host_cosign_controller_workspace_root` | no | Controller-local parent path for temporary cosign artifacts | `/tmp` |

## Example Playbook

```yaml
- hosts: ca_servers
  roles:
    - role: wtcross.step.step_host
      vars:
        step_host_update_packages: true
        step_host_reboot_if_required: true
```

## License

MIT-0
