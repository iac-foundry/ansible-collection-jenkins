# ansible-collection-jenkins

Ansible source repository for the `blueprint.jenkins` collection.

## Scope

This collection provides reusable Jenkins automation for multi-organization use:

- `roles/controller`: Jenkins controller lifecycle automation
- `roles/agent`: Jenkins inbound agent lifecycle automation

## Reuse Contract

This repository is intentionally product-generic.

- Do not hardcode organization-specific hostnames, vault paths, DNS names, or OIDC groups in role defaults.
- Put organization-specific values in consumer repository inventory/group vars.
- Keep role behavior fail-fast when required inputs are missing.

## Migration Contract

The agent role supports controller runtime migration through `jenkins_agent_hosts_controller_mode`:

- `container`
- `systemd`

This allows staged migration of consumers without forcing immediate controller runtime changes.
