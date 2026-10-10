# webstore-manifests

Kubernetes manifests.

## Where things live

- `apps/` — Argo CD `Application` objects, one per component. Auto-synced to
  the `web-platform` cluster; changes land only through pull requests.
- `components/` — the manifests those Applications deploy.

## Feature flags are not in this repo

The shop services and the load generator read feature flags from flagd
(`components/flagd`). The flag **values** (`shop/flagd-config` ConfigMap,
`flags.json`) are owned by the `flagd-values` Application
(`apps/flagd-values.yaml`) and synced from the `asimerel/platform-config`
repository, path `flagd-values`. To change a flag, open a PR there — one
flag change per commit.

flagd hot-reloads `flags.json`, so a flag change takes effect within about a
minute of the Argo sync with no pod restart. If a shop service changes
behaviour with no commit here, check `kubectl logs -n shop deploy/flagd` for a
`filepath event: /etc/flagd/flags.json` line near the onset and compare the
live ConfigMap against the expected defaults
(`kubectl get cm flagd-config -n shop -o jsonpath='{.data.flags\.json}'`).
