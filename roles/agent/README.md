# blueprints.jenkins.agent

Deploys a Jenkins inbound build agent as a `systemd` service on the target host.

The role installs Java, creates a dedicated system user, downloads `agent.jar` from
the controller, writes the JNLP secret to a protected file (mode 0600, never in
process argv), and manages the `jenkins-agent.service` unit.

## Deployment model

`systemd` service — the agent process runs under a dedicated `jenkins` system user
and reconnects automatically on failure.  Requires a host with `systemd` (Linux only).

## Required inputs (no defaults — caller must supply)

| Variable | Description |
|---|---|
| `jenkins_agent_server_url` | Full URL of the Jenkins controller (e.g. `https://ci.example.com`) |
| `jenkins_agent_name` | Node name as registered in the Jenkins controller |

## Key optional inputs

| Variable | Default | Description |
|---|---|---|
| `jenkins_agent_secret` | `""` | JNLP join secret; resolved and passed in by caller; `no_log` |
| `jenkins_controller_mode` | `container` | `container` or `systemd`; fail-fast on invalid value |
| `jenkins_agent_use_websocket` | `true` | Connect via WebSocket (recommended); set `false` for raw JNLP port |
| `jenkins_agent_validate_certs` | `true` | TLS cert validation; set `false` only in test environments |
| `jenkins_agent_java_package` | `openjdk-21-jre-headless` | Java package name on the target OS |
| `jenkins_agent_work_dir` | `/var/lib/jenkins-agent` | Working directory for workspace and jar |
| `jenkins_agent_user` | `jenkins` | System user the agent process runs as |
| `jenkins_agent_extra_jvm_args` | `""` | Extra JVM flags (e.g. proxy settings, heap size) |

See `meta/argument_specs.yml` for the complete typed interface.

## Non-goals

- Does not retrieve the JNLP secret — the caller resolves it (from Vault, CI env vars, or
  another secret store) and passes it in as `jenkins_agent_secret`.
  See the [secret consumption standard](../../../../docs/standards/BLUEPRINTS_SECRET_CONSUMPTION.md).
- Does not configure the Jenkins controller or register the node — the node must already
  exist on the controller side before this role runs.
- Does not install Docker, Ansible, Terraform, or any CI toolchain — toolchain installation
  is a caller/platform concern and varies by organisation.
- Does not configure TLS or CA trust — CA bootstrap is a platform-layer concern.
- Cross-product wiring (e.g. Vault auth for the agent's pipelines) lives in
  `blueprints.jenkins_integrations`.
