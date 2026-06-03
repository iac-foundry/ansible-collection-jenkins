# blueprint.jenkins.controller

Reusable Jenkins controller role for multi-organization environments.

## Design intent

- Product-generic role behavior suitable for multiple consumers.
- No organization-specific assumptions in defaults.
- Consumer repositories own environment-specific values (URLs, OIDC groups, vault paths, DNS names, job catalog inputs).

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