# Description

A short summary of the changes and the motivation/context behind them. List any dependencies that are required for this change.

Fixes # (issue)

## Type of change

Please delete options that are not relevant.

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] This change requires a documentation update

## How Has This Been Tested?

Describe how you verified the change and in what environment.

- [ ] `ansible-lint playbooks/` passes with the repository profile
- [ ] `pre-commit run --all-files` passes
- [ ] Ran against a target host: `ansible-playbook <playbook>.yml --limit <host> --start-at-task ...`
- [ ] Only ran `ansible-playbook ... --syntax-check` (playbook can't be run in CI)

**Details**:

- ansible-core version:
- ansible-lint version:
- Target OS (if applicable):

## Checklist:

- [ ] My code follows the style guidelines of this project (2-space indent, yamllint/prettier clean)
- [ ] I have performed a self-review of my code
- [ ] I have made corresponding changes to the documentation (README, inventory examples)
- [ ] If I bumped/added a `roles/*` submodule, I updated the gitlink
- [ ] My changes generate no new `ansible-lint` warnings
- [ ] I have not committed any secrets (passwords, ssh keys, password hashes) -- replace with placeholders
- [ ] I have run `ansible-playbook ... --syntax-check` at least
