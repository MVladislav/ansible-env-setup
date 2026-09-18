# Contributing

Thank you for considering a contribution to **Ansible Env Setup**.

## Development environment

The project uses a local Python virtual environment plus `pre-commit`.

```sh
python3 -m venv .venv
.venv/bin/pip install -U pip
.venv/bin/pip install -r requirements.txt     # points-based, pinned in requirements.txt

# install the git hooks
.venv/bin/pip install pre-commit
pre-commit install
```

`requirements.txt` is pinned and must stay in sync with the versions used in [`.pre-commit-config.yaml`](.pre-commit-config.yaml)
(see the `ansible-lint` hook's `ansible-core` and `rev:` entries).

## Repository conventions

- Every `*.yml` file is 2-space indented (see [`.yamllint`](.yamllint), [`.editorconfig`](.editorconfig) and Prettier).
- Secrets never go into the repository. Inventory examples ship with placeholders only (`ansible_user: <USERNAME>`,
  `password: "$6$..."`). Use `mkpasswd --method=sha-512` locally for real password hashes and keep them in your private inventory
  files (`inventory/*.yml` is git-ignored on purpose).
- Common variables live in [`inventory/inventory-default.yml`](inventory/inventory-default.yml); per-host overrides (dicts like
  `pl_a_user_config`, `pl_a_clients`) must stay in the per-device file because host vars replace, not merge.

## Roles

`roles/*` are **git submodules** of separate repositories, not maintained here.

- To add a new role collection: `git submodule add <url> roles/<name>` (pinned to a tag/commit, not
  `git submodule update --remote` on every run).
- After bumping a submodule commit, the new gitlink is part of your PR.
- Playbooks reference roles through `roles_path = ./roles` in `ansible.cfg`.

## Collections

Extra Galaxy collections are declared in [`requirements.yml`](requirements.yml). Install them with:

```bash
ansible-galaxy collection install -r requirements.yml
```

## Before you submit

Run the full local gate:

```bash
.venv/bin/ansible-lint playbooks/
.venv/bin/yamllint playbooks/ inventory/ .github/ .pre-commit-config.yaml
.venv/bin/pre-commit run --all-files
```

and if you touched playbooks, at least a syntax/parse check:

```bash
.venv/bin/ansible-playbook playbooks/playbook_*.yml --syntax-check
```

A full runtime test can be done against this machine safely for the security baseline without the reboot step (see
`inventory/localhost.yml`):

```bash
.venv/bin/ansible-playbook playbooks/playbook-s-pre-install.yml --limit localhost -K
.venv/bin/ansible-playbook playbooks/playbook-s-cis.yml --limit localhost -K
.venv/bin/ansible-playbook playbooks/playbook-s-hardening.yml --limit localhost -K
```

`playbook-sec-short.yml` ends with a reboot -- run it only when you accept that.

## Pull request

Use the template in `.github/PULL_REQUEST_TEMPLATE.md`. In short:

- Describe the change and why.
- Run `ansible-lint` and `pre-commit` locally and confirm.
- Update the README flow/diagrams if the box table content changes.
- Never include real secrets.

## Reporting security issues

Do **not** open a public issue. Use the private security advisory flow, see [`SECURITY.md`](SECURITY.md).
