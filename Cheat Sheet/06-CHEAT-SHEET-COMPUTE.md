# AZ-104 cheat sheet 3/5 — Deploy and manage Azure compute resources

*One page. Facts snapshot 2026-11; verify current behaviour on Microsoft Learn before the exam.*

## ARM templates and Bicep

| Item | What to remember |
|---|---|
| Required sections | `$schema`, `contentVersion`, `resources`. `parameters`, `variables`, `outputs` are optional. |
| Idempotency | Redeploying the same template with the same parameters produces the same result; existing matching resources are left alone. |
| Deployment modes | **Incremental** (default; keeps resources not in the template) vs **Complete** (deletes them). Complete deletes resources in the scope, so use a dedicated RG. |
| Parameters | `secureString` and `secureObject` are not logged. Use `allowedValues` to constrain input and `defaultValue` to make a parameter optional. |
| Dependencies | `dependsOn` for explicit ordering. Use `reference()`/`resourceId()` for implicit dependencies and outputs; circular dependencies fail validation. |
| Scope | `resourceGroup` (default), `subscription`, `managementGroup`, `tenant`. Nested or linked templates change the scope. |
| What-if | Preview create/modify/delete/ignore before deployment. Available in CLI, PowerShell, and the portal. |
| Bicep | Compiles to ARM JSON. `az bicep build` / `bicep build`. `bicep decompile` is best-effort and usually needs cleanup. |
| Modules | One Bicep file per reusable unit; pass params, take outputs. Registry modules come from ACR. |
| Export | Portal **Export template** only covers resources in that RG and loses some properties; not a substitute for source control. |

## Virtual machines

| Item | What to remember |
|---|---|
| Sizes | B (burstable) · D (general) · E (memory optimised) · F (compute optimised) · N (GPU) · L (storage). The letter is the family, the number is the vCPU count. |
| Resize | Requires a **deallocate** first unless the new size is available on the same host. Cross-family resize may need a redeploy. |
| Disks | OS disk (Premium SSD, Standard SSD, Standard HDD, ephemeral) · data disks · **Ultra disk** (needs a zone, no host caching, only certain sizes). |
| Ephemeral OS disk | Uses local VM storage; no storage cost; lost on deallocate. Good for stateless scale sets. |
| Disk caching | `ReadOnly` suits read-heavy data; `None` for write-heavy or Ultra. **ReadWrite** is not allowed on some sizes. |
| Encryption | Azure Disk Encryption (BitLocker/DM-Crypt, uses Key Vault) vs **encryption at host** vs server-side encryption with a CMK. ADE requires a key vault. |
| Extensions | Custom Script, DSC, Azure Monitor Agent, VM Backup. Order with `dependsOn`; the agent runs as SYSTEM/root. |
| Networking | Each VM needs a NIC in a subnet. NSG can sit on the subnet or the NIC; both are evaluated. |
| Availability set | Up to **3 fault domains** and **20 update domains** (default 5). Protects against host and rack failure inside one datacenter only. |
| Availability zone | Physically separate datacenter in the region; **three** in every enabled region. Protects against a datacenter loss. |
| SLA | Single VM with premium disks: **99.9%** · availability set: **99.95%** · zones: **99.99%**. |

## Scale sets

| Item | What to remember |
|---|---|
| Orchestration | **Uniform** (identical instances, VMSS-managed) vs **Flexible** (mix of VMSS and standalone VMs, no uniform guarantees). |
| Zone spanning | A uniform set can span zones (max 3) but **not** be both zonal and regional. |
| Autoscale | Metric rules (CPU, memory via agent, queue length) or schedule. Set min, max, and default count. |
| Cool-down | After a scale action the rule waits `cooldown` (default 5 min) before acting again. |
| Scale-in policy | Default · Newest VM · Oldest VM. Protect instances that must not be removed. |
| Upgrade policy | **Manual** · **Automatic** (all at once, may cause downtime) · **Rolling** (batch + pause, honours health probes) · **Canary** (Flexible). |
| Overprovisioning | Creates extra instances then deletes them; speeds deployment but costs briefly. Disable for large or costly SKUs. |
| Health | Application health extension or a load balancer probe drives rolling upgrades and automatic instance repair. |

## Containers

| Item | What to remember |
|---|---|
| ACI | Serverless container group; fastest to start; no orchestrator; per-second billing. Good for short batch jobs and burst. |
| Container group | One or more containers sharing a host, network namespace, and storage. Restart policy: Always, OnFailure, Never. |
| ACI networking | Optional VNet injection with a delegated subnet; otherwise a public IP or no IP at all. |
| ACR | Private registry. Tiers: Basic, Standard, Premium (geo-replication, private link, content trust). |
| ACR auth | `docker login` with the admin account (avoid), a service principal, a managed identity, or `az acr login`. |
| Container Apps | Managed Kubernetes-based serverless platform: scale to zero, revisions, traffic splitting, Dapr, KEDA rules. |
| AKS | Full Kubernetes control; node pools, upgrade channels. AZ-104 only needs the basics. |

## App Service

| Item | What to remember |
|---|---|
| Plans | Free/Shared (no SLA) · Basic · Standard · Premium v3 · Isolated (ASE). Scale up = change plan; scale out = instance count. |
| Slots | Standard or higher. A **swap** warms the target slot then switches; slot settings (connection strings, app settings marked "slot setting") stay put. |
| Custom domain | CNAME for a subdomain, **A record + `asuid` TXT** for the apex. Verify before adding. |
| TLS | App Service managed certificate (free, no wildcard, no apex) or upload a PFX. Enforce HTTPS with a redirect. |
| Networking | Access restrictions (allow/deny by IP or VNet) · VNet integration (outbound) · private endpoint (inbound) · Hybrid Connections. |
| Backup | Needs Standard or higher and a storage account **in the same subscription**. Backs up app content and configuration, not the database by default. |
| Deployment | Zip deploy, GitHub Actions, Azure DevOps, `az webapp deploy`, run-from-package. |
| Always On | Keeps the app warm; required for continuous WebJobs. Not available on Free/Shared. |
