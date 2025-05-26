# Ansible Role OSCAP

> Audit and remediate Linux system with OpenSCAP.


## Key features

* Three execution modes: **Audit**, **Remediation** or **Package‑Install‑Only**
* Creates **JSON, ARF and HTML** reports with time stamps for every run
* Generates host‑specific Ansible playbooks from `oscap generate fix` and executes them
* Fine‑grained control: skip whole phases *or* single rules via variables
* Works offline if RPMs are pre‑staged

## Supported operating systems

| Distribution | Versions | Notes                                                 |
| ------------ | -------- | ----------------------------------------------------- |
| AlmaLinux    | 8, 9     | Other RHEL derivatives *should* work but are untested |

## Role variables (excerpt from `defaults/main.yml`)

| Variable                          | Default                                                 | Description                                    |
| --------------------------------- | ------------------------------------------------------- | ---------------------------------------------- |
| `hardener_audit_system`           | `true`                                                  | Execute **audit** via `oscap xccdf eval`       |
| `hardener_remediate_system`       | `false`                                                 | Run **remediation** via generated fix playbook |
| `hardener_manage_packages`        | `true`                                                  | Install OpenSCAP, SSG, jq if missing           |
| `hardener_profile`                | `xccdf_org.ssgproject.content_profile_cis_server_l1`    | Change to Level 2 by selecting the L2 profile  |
| `hardener_ssg_ds_file`            | `/usr/share/xml/scap/ssg/content/ssg-almalinux9-ds.xml` | Override if path differs                       |
| `hardener_output_dir`             | `/var/log/hardener`                                     | Where all reports are saved                    |
| `hardener_ignore_remediate_tasks` | `[]`                                                    | List of rule‑tags **not** to remediate         |

Check `defaults/main.yml` for the full list.

## Quick start

```yaml
---
- hosts: almalinux
  become: yes
  roles:
    - role: fsp123361.ansible-role-oscap
      vars:
        hardener_remediate_system: true
```

```bash
ansible-playbook -i inventory harden.yml
```

The HTML report will be written to `/var/log/hardener/<timestamp>-report.html`.


## Requirements

* **Ansible ≥ 2.14** on the control node
* Target host: AlmaLinux 8 or 9
* Packages: `openscap-scanner`, `scap-security-guide`, `openscap-utils`, `jq`
  (installed automatically when `hardener_manage_packages` is `true`)

## Limitations

* Default datastream path is for AlmaLinux 9; adjust variable for AlmaLinux 8 or other distros.
* Coverage depends on the version of **SCAP Security Guide** available in your repos.
* The role assumes a Bash environment; zsh setups may need tweaks.

## Roadmap

* Auto‑detect distribution and pick the correct datastream automatically
* Molecule tests for audit & remediation

## Contributing

Pull requests and issues are very welcome. Please follow the coding guidelines in this repository.

## License

MIT License – see `LICENSE`.

## References

* CIS AlmaLinux 8 Benchmark v1.0.0
* CIS AlmaLinux 9 Benchmark v1.0.0
* SCAP Security Guide Project
