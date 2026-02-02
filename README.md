# awx_host_prep

Role that prepares a Debian/Ubuntu host to run AWX on MicroK8s. It installs required packages, enables MicroK8s add-ons, captures kubeconfig for the control user, and validates the cluster readiness before handing off to the AWX operator.

## Requirements

- Target host must run a supported Debian or Ubuntu release with `snapd` available.
- Ansible 2.15+ with Python 3 on the control node.
- SSH access with privileges to escalate (`become: true`).

## Role Variables

| Variable | Default | Description |
| --- | --- | --- |
| `awx_host_admin_user` | `{{ ansible_user }}` | System account that will interact with MicroK8s. |
| `awx_host_base_packages` | List incl. `snapd`, `python3-pip`, etc. | Base packages to ensure present. |
| `awx_microk8s_channel` | `1.30/stable` | Snap channel for MicroK8s. |
| `awx_microk8s_addons` | `['dns', 'storage', 'ingress']` | Add-ons enabled after MicroK8s install. |
| `awx_host_kubeconfig_path` | `/home/{{ awx_host_admin_user }}/.kube/config` | Kubeconfig path exported for the admin user. |
| `awx_host_kubectl_link` | `/usr/local/bin/kubectl` | Symlink target for kubectl. |
| `awx_host_helm_link` | `/usr/local/bin/helm` | Symlink target for helm. |
| `awx_host_ingress_firewall_allow` | `true` | When true and ufw is active, allow TCP 80 and 443 so ingress is reachable from outside. Does not enable ufw. |

See `defaults/main.yml` for the full list of tunables.

## Dependencies

- When `awx_host_ingress_firewall_allow` is true (default), the play must have the **community.general** collection available (for the `ufw` module). Declare it in the play's `collections:` or install with `ansible-galaxy collection install community.general`.

## Example Playbook

```yaml
- name: Prepare AWX host
  hosts: awx-rone
  gather_facts: true
  roles:
    - role: awx_host_prep
      vars:
        awx_host_admin_user: srvadm
```

## License

GPL-3.0-or-later

## Author Information

Mauro Rosero P. (Libre Technology)

