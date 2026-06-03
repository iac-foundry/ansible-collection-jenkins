# blueprint.jenkins.controller

Reusable Jenkins controller role for multi-organization environments.

## Design intent

- Product-generic role behavior suitable for multiple consumers.
- No organization-specific assumptions in defaults.
- Consumer repositories own environment-specific values (URLs, OIDC groups, vault paths, DNS names, job catalog inputs).
- Minimal-first controller: base HTTP controller can be deployed without SSL, Vault, OIDC, or SMTP.

## Integration model

Optional integrations are enabled only when explicitly configured:

- `jenkins_proxy_enabled`: integrate with a host proxy role (recommended as a separate shared role, for example in `blueprint.common`).
- `jenkins_oidc_enabled`: enable OIDC security realm.
- `jenkins_escape_hatch_enabled`: enable local emergency admin.
- `jenkins_mailer_enabled`: enable SMTP mailer configuration.

Secret sourcing is explicit and caller-controlled:

- OIDC: `jenkins_oidc_secret_source` in `vars|env|vault`
- Escape hatch: `jenkins_escape_hatch_secret_source` in `vars|env|vault`

For `vault` source, caller supplies Vault address/path variables; no platform-specific paths are hardcoded.

## Consumer responsibilities

Set these in consumer inventory/group vars before deployment:

- `jenkins_root_url`
- `jenkins_proxy_server_name`
- `jenkins_oidc_well_known_url`
- `jenkins_vault_oidc_path` and related vault fields
- `jenkins_global_shared_libraries`
- `jenkins_jobdsl_folders` and `jenkins_jobdsl_pipelines`

## Important

This role is intentionally reusable and must not be coupled to a single organization.
If a default contains organization identity, move it to consumer configuration.