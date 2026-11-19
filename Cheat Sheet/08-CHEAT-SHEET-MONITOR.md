# AZ-104 cheat sheet 5/5 — Monitor and back up Azure resources

*One page. Facts snapshot 2026-11; verify current behaviour on Microsoft Learn before the exam.*

## Azure Monitor data

| Item | What to remember |
|---|---|
| Metrics | Numeric time series, near real time. **Platform** metrics are automatic; **guest** metrics need the Azure Monitor Agent and a data collection rule. |
| Metric retention | **93 days** at minute granularity. Extend by archiving to storage or sending to Log Analytics. |
| Activity log | Control-plane events (who did what to which resource). Kept **90 days**; export to Log Analytics, storage, or Event Hubs for longer. |
| Resource log | Data-plane and service-specific logs ("diagnostic logs" in older docs). Off by default; needs a diagnostic setting. |
| Diagnostic setting | One per resource (up to five) sending to **Log Analytics**, **storage**, **Event Hubs**, or a partner. Subscriptions can use a policy to deploy them at scale. |
| Log Analytics | Workspace-based log store queried with KQL. Retention: 30 days interactive by default, up to 730; archive tier beyond that. |
| Workspace-scoped query | `workspace('name').Table` queries another workspace without leaving yours. |
| VM insights | Guest performance and dependency map. Needs AMA + DCR; the legacy MMA agent is retired. |
| Container insights | Node, pod, and container metrics and logs for AKS and Arc. |

## KQL essentials

| Operator | What it does |
|---|---|
| `where` | Filter rows. `| where TimeGenerated > ago(1h) and Level == "Error"` |
| `summarize` | Aggregate: `summarize count() by Computer`, `avg(CPU) by bin(TimeGenerated, 5m)` |
| `bin()` | Buckets a time column into fixed intervals for charts. |
| `project` | Choose and rename columns; `project-away` removes them. |
| `extend` | Add a computed column. |
| `join` | Combine tables (`kind=inner`, `leftouter`, …) on a key. |
| `take` / `limit` | Sample rows for exploration. |
| `order by` | Sort; add `desc` for newest first. |
| `_ResourceId` | Scopes results to a resource when a workspace holds many. |
| Time filters | `ago(24h)`, `between (datetime(…) .. datetime(…))`, `startofday()`. |

## Alerts

| Item | What to remember |
|---|---|
| Signal types | Metric · log (Log Analytics) · activity log · smart detection · Prometheus (managed). |
| Metric alert | Threshold on a metric, evaluated every 1 minute by default. Multi-resource alerts apply one rule to many resources with dimensions. |
| Log alert | KQL result count or value. Can use **dimensions** to split per computer. Minimum frequency 1 minute (5 for some). |
| Activity log alert | Triggered by control-plane events such as service health or resource delete. |
| Action group | Reusable set of notifications (email, SMS, voice, push) and actions (Logic App, Function, webhook, Automation Runbook, Event Hubs, ITSM). |
| Alert processing rule | Suppress or route alerts during a maintenance window, or apply an action group to a filtered set. |
| Severity | 0 critical → 4 verbose. Severity is part of the alert rule, not the action group. |
| State | New · Acknowledged · Closed. Fired alerts auto-resolve when the condition clears (metric alerts). |

## Monitoring agents

| Item | What to remember |
|---|---|
| AMA | Azure Monitor Agent replaces MMA/OMS. One agent, many DCRs. |
| DCR | Data collection rule: what to collect, how to transform it, where to send it. Associated with VMs, VMSS, or Arc servers. |
| Multiple DCRs | Several DCRs can apply to one VM; the union of their data sources is collected. |
| Syslog / Windows events | Configured in the DCR, not on the agent. |
| Association | DCR associations can be created per VM or at scale with policy. |

## Network Watcher

| Tool | Use it for |
|---|---|
| IP flow verify | Allow/deny decision and the NSG rule responsible. |
| Next hop | Effective route and next-hop type for a destination. |
| Connection troubleshoot | One-shot reachability test with latency. |
| Connection Monitor | Continuous tests with alerts, across VMs, endpoints, and ExpressRoute. |
| Packet capture | Capture traffic on a VM NIC or through an NSG. |
| Flow logs | NSG flow logs (now VNet flow logs) record allowed and denied flows to storage. |
| Topology | Visual network map for a resource group. |

## Backup

| Item | What to remember |
|---|---|
| Vault choice | **Recovery Services vault** for IaaS VMs, SQL in VM, Azure Files, SAP HANA. **Backup vault** for blobs, managed disks, PostgreSQL, AKS, and vaulted (enhanced) policies. |
| Redundancy | LRS, ZRS, GRS (default). **Cross-region restore** must be enabled on the vault before you need it. |
| VM backup | Snapshot-based, application-consistent where possible. Policy sets frequency (daily/weekly), instant restore tier, and retention. |
| Enhanced policy | More frequent backups (every 4 hours), multiple daily recovery points, longer instant restore. |
| Retention | Daily, weekly, monthly, yearly rules. The longest matching rule wins; retention is calculated from the backup time. |
| Restore options | Replace existing · Create new (alternate location) · Restore disks · File recovery (mount a recovery point as a drive, 12-hour limit). |
| Azure Files backup | Snapshot-based; the share must be in a storage account the vault can reach. |
| Blob backup | **Operational backup** (local, continuous, 1–360 days) or **vaulted backup** (copy to a vault, cross-region). |
| Soft delete | 14 days by default for backup items; **always** enable enhanced soft delete. |
| Security | Multi-user authorization, immutable vault, MFA for critical operations, alert on delete. |

## Site Recovery

| Item | What to remember |
|---|---|
| Purpose | Disaster recovery replication to a secondary region (or on-premises to Azure), with orchestrated failover. |
| Replication | Continuous, per VM, into a cache storage account in the target region. RPO typically 5–15 minutes. |
| Recovery plan | Groups VMs, adds pre/post scripts and manual steps, and sets boot order. |
| Test failover | Isolated test network, no impact on production, no replication stop. Always test before a real failover. |
| Failover | Planned (no data loss), unplanned (possible data loss), or test. Commit or re-protect afterwards. |
| Targets | Azure VMs, Hyper-V, VMware, physical servers. Requires the Mobility service agent on each source. |
