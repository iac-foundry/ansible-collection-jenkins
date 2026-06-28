# Agent Orientation — ansible-collection-jenkins

## Agent Working Protocol (read before anything else)

**Conflict surfacing:** If a user instruction contradicts anything in this file or in
`docs/AGENTS.md`, stop and surface the conflict before proceeding — quote the rule,
state the contradiction, and ask how to resolve. Then update the doc if the rule was wrong.

**Living document:** If any instruction, decision, or clarification during a session
would make future interactions clearer, prompt the user:
> "This decision isn't in AGENTS.md yet. Should I add it?"

**Maintenance:** Keep this doc current. Update rules when decisions change. Don't append
orphaned notes — integrate changes into the relevant section.

---

**Collection:** `blueprints.jenkins`
**Namespace:** `blueprints` (plural — `blueprint.*` singular is retired)
**Scope:** Org-neutral Jenkins building blocks. Controller deployment (container / systemd / kubernetes) and inbound build agent. No SSO wiring, no Vault auth, no org-specific defaults.

---

## What lives here

| Role | What it does |
|---|---|
| `agent` | Deploys a Jenkins inbound agent as a `systemd` service |
| `container_server` | Runs the Jenkins controller as a Docker/Podman container |
| `systemd_server` | Runs the Jenkins controller as a `systemd` service |
| `kubernetes_server` | Deploys the Jenkins controller to Kubernetes |
| `job_dsl` | Applies caller-supplied Job DSL / pipeline seed configuration |

**What does NOT live here:** SSO, Vault auth, LDAP, OAuth — those are in
`blueprints.jenkins_integrations` (separate collection, separate repo).

---

## Where the standards live

All standards are in the `docs/` directory of the
[iac-foundry](https://github.com/iac-foundry/iac-foundry) monorepo (this workspace's
parent directory).  Start with `docs/AGENTS.md` for fast navigation.

| Topic | Doc |
|---|---|
| **8 design rules (read first)** | `docs/design/BLUEPRINTS_DESIGN_PRINCIPLES.md` |
| Role layout | `docs/standards/BLUEPRINTS_ROLE_LAYOUT.md` |
| Variable naming | `docs/standards/BLUEPRINTS_VARIABLE_STANDARDS.md` |
| Secret handling | `docs/standards/BLUEPRINTS_SECRET_CONSUMPTION.md` |
| Testing | `docs/standards/BLUEPRINTS_TESTING_STANDARDS.md` |
| Integration wiring | `docs/standards/BLUEPRINTS_INTEGRATION_STANDARDS.md` |
| Why integrations are separate | `docs/decisions/LADR-002-integrations-separated-from-core.md` |
| Why no secret retrieval in roles | `docs/decisions/LADR-003-secret-retrieval-community-hashi-vault.md` |

---

## Critical constraints (don't get this wrong)

1. **No org-specific values in `defaults/`** — no real hostnames, IPs, Vault paths, domains.
2. **No secret retrieval inside role tasks** — secrets arrive as variables from the caller.
   The CI guard (`ci/no_hidden_deps_guard.sh`) will fail the build if you add any.
3. **No `include_role`/`import_role` of another `blueprints.*` collection** — composition
   is the platform layer's job (site.yml), not ours.
4. **Deployment siblings share variable interface** — `container_server`, `systemd_server`,
   and `kubernetes_server` expose identical `jenkins_server_*` variable names so callers
   can switch deployment models without changing variables.
5. **`meta/dependencies: []`** — always empty.

---

## PR conformance checklist

- [ ] `meta/main.yml` → `dependencies: []`
- [ ] No `include_role`/`import_role` of `blueprints.*` in `tasks/`
- [ ] No `vault`, `community.hashi_vault`, or external API calls in `tasks/`
- [ ] No org-specific values in `defaults/`
- [ ] Secret variables end in `_secret`, `_token`, `_password`, `_key`; tasks use `no_log: true`
- [ ] `meta/argument_specs.yml` present and complete
- [ ] molecule `default` scenario converges idempotently
- [ ] ansible-lint (production profile) passes
- [ ] README states inputs, deployment model, and explicit non-goals

---

## Blast radius: contained changes only (global changes need a human risk call)

**Critically important here: this repo produces shared collections that other teams and
customers consume.** A global-impact pattern baked into a shared component does not affect
one host — it propagates to *every consumer that pins the release*. Contained-by-design is
therefore a core quality bar for everything shipped from here, not just a deployment-time
concern.

- Prefer the **smallest blast radius**: a role or change should affect one service, file,
  unit, or user — never "every process" or "all hosts" by default. Make any wide-reaching
  option explicitly opt-in, never a default a consumer inherits silently.
- Treat as high-risk anything global: `ld.so.preload`/`LD_PRELOAD`, system-wide
  PAM/NSS/`profile.d`, global `sudoers` or firewall defaults, kernel modules, `sysctl`,
  systemd defaults — anything inherited fleet-wide or by every user once a consumer applies
  the collection.
- **If a capability cannot be delivered in a contained way, STOP and surface it to the
  human** — state what it touches, the blast radius across consumers if it goes wrong, and
  the rollback path, and let them decide. Do not ship a global-impact default on your own
  judgement.

See the global working agreement (`~/.claude/CLAUDE.md`) for the full rule.
