# migration-station and state keeper : TODO build and push up code for 


# Rework Existing Project — statekeeper

**Use with:** Claude Code, pointed at the existing database monitoring repo  
**Repo:** Will be renamed/published as `YusefAmen/statekeeper`

---

I'm doing a professional overhaul of this project. It's an existing but incomplete codebase — a database monitoring and reaction engine for Kubernetes StatefulSets. The original project was never completed to a working state. I need it finished, tested, and made professional.

## Step 1: Audit the Current State

Before changing anything, give me a full readout:

- Project structure (tree)
- What exists and what state it's in
- What was the original intent of each module?
- What works, what's partially built, what's just scaffolded?
- Dependency state
- Any tests?
- How far from "working" is this?

## Step 2: Define What "Done" Looks Like

Statekeeper is a monitoring and reaction engine for databases running as Kubernetes StatefulSets. Its job is to watch database health, detect problems, and take automated corrective actions.

### Core Functionality (must all work with tests)

**Monitoring:**
- Connect to Kubernetes API, watch StatefulSets
- Poll database health metrics (connection count, replication lag, disk usage, query performance)
- Support for PostgreSQL as the primary target
- TODO stub: MySQL support
- TODO stub: MongoDB support

**Detection (rules engine):**
- Rule-based detection of common database issues:
  - Replication lag exceeding threshold
  - Connection pool exhaustion
  - Disk usage approaching capacity
  - Long-running queries
  - Pod restart loops (CrashLoopBackOff detection)
- Rules follow a consistent pattern with a base class
- TODO stubs: 2-3 additional rules following the same pattern

**Reactions (automated responses):**
- Alert via webhook (Slack, PagerDuty, generic)
- TODO stub: Automated failover trigger
- TODO stub: Connection kill for long-running queries
- TODO stub: Scale storage (PVC resize)
- Each reaction follows a consistent pattern with a base class

**Configuration:**
- YAML config file defining:
  - Which StatefulSets to watch
  - Database connection details (or auto-discovery from K8s secrets)
  - Rules and their thresholds
  - Reaction mappings (which rules trigger which reactions)
- Env var overrides for sensitive values

### CLI Interface

```bash
# Run the monitor
statekeeper watch --config statekeeper.yaml

# One-shot health check
statekeeper check --config statekeeper.yaml

# Validate config
statekeeper validate --config statekeeper.yaml

# List discovered StatefulSets
statekeeper discover --namespace database
```

### Architecture

```
statekeeper/
├── src/
│   └── statekeeper/
│       ├── __init__.py
│       ├── cli.py
│       ├── config.py
│       ├── kubernetes/         ← K8s API interaction
│       │   ├── __init__.py
│       │   ├── client.py       ← K8s client wrapper
│       │   ├── discovery.py    ← StatefulSet discovery
│       │   └── watcher.py      ← Watch loop
│       ├── databases/          ← Database-specific health checks
│       │   ├── __init__.py
│       │   ├── base.py         ← Abstract database monitor
│       │   ├── postgresql.py   ← PostgreSQL health checks
│       │   └── # TODO stubs: mysql.py, mongodb.py
│       ├── rules/              ← Detection rules
│       │   ├── __init__.py
│       │   ├── base.py         ← Abstract rule
│       │   ├── replication_lag.py
│       │   ├── connection_pool.py
│       │   ├── disk_usage.py
│       │   ├── long_queries.py
│       │   ├── crashloop.py
│       │   └── # TODO stubs: 2-3 more following pattern
│       └── reactions/          ← Automated responses
│           ├── __init__.py
│           ├── base.py         ← Abstract reaction
│           ├── webhook.py      ← Slack/PagerDuty/generic webhook
│           └── # TODO stubs: failover.py, kill_query.py, resize_pvc.py
├── tests/
│   ├── conftest.py             ← Mock K8s client, mock DB connections
│   ├── test_kubernetes/
│   ├── test_databases/
│   ├── test_rules/
│   └── test_reactions/
├── examples/
│   └── statekeeper.yaml        ← Example config file
├── pyproject.toml
├── Dockerfile
├── helm/                       ← Helm chart for deploying in K8s
│   └── statekeeper/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
├── .github/workflows/ci.yml
├── README.md
├── LICENSE (MIT)
├── CHANGELOG.md
├── CONTRIBUTING.md
└── .gitignore
```

## Step 3: Implement

- Salvage whatever is usable from the existing code
- Refactor into the architecture above
- All core functionality must work: discover StatefulSets → monitor health → detect issues → fire webhooks
- PostgreSQL is the primary supported database — this must work end-to-end
- Mock K8s API and database connections in tests
- All tests pass with `pytest`
- TODO stubs follow the exact same pattern as implemented modules
- Include a Helm chart for deploying statekeeper itself as a pod in the cluster

## Step 4: Professional README

Include:
- Clear one-liner: "A database monitoring and reaction engine for Kubernetes StatefulSets"
- Tagline: "Because databases in K8s deserve more than a liveness probe."
- Badges (CI, Python version, license)
- Architecture diagram (Mermaid) showing: K8s API → Statekeeper → Rules Engine → Reactions
- Quick start (local + Helm install)
- Configuration reference with annotated example YAML
- Full rules reference
- Supported databases table (PostgreSQL ✅, MySQL 🔜, MongoDB 🔜)
- Roadmap listing TODO stubs
- Author section: "Built by Jared (Yusef) Amen — SRE & platform engineering consultant"
- Buy Me a Coffee: https://buymeacoffee.com/YusefAmen
- GitHub Sponsors: https://github.com/sponsors/YusefAmen

Tone: This is a serious infrastructure tool. Professional, confident, clear. Less personality than the other repos — more like the tone of a CNCF project README.

## Key Constraints

- Must work with standard Kubernetes RBAC
- No hardcoded credentials anywhere
- All K8s and DB interactions must be mockable for testing
- Helm chart must be valid and installable
- Python 3.9+
