# ansible-collection-jenkins

Ansible source repository for the `blueprint.jenkins` collection.

## Scope

This collection provides reusable Jenkins automation for multi-organization use:

- `roles/controller`: Jenkins controller lifecycle automation
- `roles/agent`: Jenkins inbound agent lifecycle automation

The controller role follows a minimal-first model and can stand up a base controller
without SSL/Vault/OIDC/SMTP. Optional integrations are enabled by explicit flags.
Companion integrations (for example host proxy lifecycle) should live in dedicated
shared roles/collections, rather than being hard-wired into the base controller role.

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
