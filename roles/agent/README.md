# blueprint.jenkins.agent

Reusable Jenkins inbound agent role for multi-organization environments.

## Design intent

- Multi-org reusable: no organization-specific hostnames, DNS names, Vault paths, or OIDC assumptions in defaults.
- Fail-fast: required configuration must be supplied by consumer inventory/group vars.
- Migration-safe: supports both controller runtime models through `jenkins_agent_hosts_controller_mode`.

## Controller mode

`jenkins_agent_hosts_controller_mode` supports:

- `container`
- `systemd`

Consumer repos should set this explicitly during migration and enforce one value per environment.

## Important

Organization-specific values belong in consumer repositories, not in role defaults.
