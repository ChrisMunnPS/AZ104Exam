# AZ-104 cheat sheet 2/5 — Implement and manage storage

*One page. Facts snapshot 2026-11; verify current behaviour on Microsoft Learn before the exam.*

## Storage accounts

| Item | What to remember |
|---|---|
| Account kinds | **Standard general-purpose v2** (blobs, files, queues, tables) · **Premium block blobs** · **Premium file shares (FileStorage)** · **Premium page blobs**. |
| Performance tiers | Standard uses HDD-backed capacity; premium uses SSD. A standard account can change access tier per blob, not per account performance tier. |
| Replication at create | Choose LRS/ZRS/GRS/RA-GRS (or GZRS/RA-GZRS). Changing later is possible in one direction only for some paths — plan first. |
| Naming | 3–24 characters, lowercase letters and numbers only, globally unique. |
| Endpoints | `https://<account>.blob.core.windows.net`, `.file.`, `.queue.`, `.table.`. |
| Limits | 5 PiB per standard account (soft), 500 TiB per premium block blob account. |
| Network access | Public from all networks · public from **selected virtual networks and IP addresses** · **private endpoint**. Setting "selected networks" disables the other paths unless you add them. |
| Require secure transfer | Forces HTTPS. Disable only for legacy SMB 2.1 or HTTP-only clients. |
| Minimum TLS | Set to TLS 1.2 (1.3 where supported). |

## Redundancy

| Option | Copies | Survives |
|---|---|---|
| **LRS** | 3 in one datacenter | Disk and rack failure |
| **ZRS** | 3 across three zones in one region | Zone failure |
| **GRS** | LRS + 3 asynchronous copies in the paired region | Region failure (no read access until failover) |
| **RA-GRS** | GRS + read-only access to the secondary endpoint | Region failure with read access |
| **GZRS** | ZRS + 3 asynchronous copies in the paired region | Zone and region failure |
| **RA-GZRS** | GZRS + readable secondary | Zone and region failure with read access |
| File shares | Premium FileStorage supports **LRS and ZRS only** — no GRS. Azure Files has **no readable secondary endpoint**. |

## Access and security

| Item | What to remember |
|---|---|
| Shared key vs Entra | Shared key authorizes with the account keys and **bypasses RBAC and file/directory ACLs**. Set **Allow storage account key access = Disabled** to force Entra ID. |
| Azure Files identity | Entra Domain Services, Entra Kerberos (cloud-only, hybrid), or on-prem AD DS over SMB. Needs a **usage location**-style prerequisite check and the right share-level role. |
| Blob data roles | **Storage Blob Data Reader / Contributor / Owner**. Control-plane roles (Contributor) do **not** grant blob data access. |
| SAS types | **Account SAS** (services + resource types) · **Service SAS** (one service) · **User delegation SAS** (Entra-backed, blobs only, max **7 days**). |
| Stored access policy | Up to **5** per container or share. Revoke by deleting or editing the policy — a service SAS without a policy cannot be revoked early. |
| SAS best practice | Short expiry, least permissions, HTTPS only, stored access policy when revocation matters, user delegation over account key. |
| Keys | Two keys per account for rotation. **Rotate** in Key Vault-backed flows; regenerating a key invalidates every SAS and client using it. |
| Encryption | Platform-managed keys by default; **customer-managed keys** in Key Vault for blob/file encryption at rest; **infrastructure encryption** adds a second layer. |
| Encryption scope | Applies a distinct CMK to a container or blob instead of the whole account. |

## Azure Files

| Item | What to remember |
|---|---|
| Protocols | SMB 3.x (Windows/Linux/macOS) and NFS 4.1 (premium only, no Windows, no identity-based auth). |
| Tiers | Transaction optimized · Hot · Cool (standard only); premium is provisioned by capacity. |
| Snapshots | Read-only point-in-time copies of a share. Restore a file from a snapshot with **Previous Versions** or `Restore-AzStorageFileCopy`. |
| Soft delete | Retains deleted shares for 1–365 days; file-level soft delete is separate. |
| Backup | Azure Backup for Azure Files uses a **snapshot-based** policy; the vault can be Recovery Services or Backup vault. |
| Limits | 100 TiB per share on standard (large file shares enabled) and premium; IOPS scale with provisioned size on premium. |

## Blobs

| Item | What to remember |
|---|---|
| Access tiers | Hot · Cool (30-day min) · Cold (90-day min) · Archive (180-day min, offline). Early deletion charges apply. |
| Rehydrate | Archive → Hot/Cool is **Standard** (hours) or **High** (under an hour, higher cost). |
| Lifecycle policy | Rules with filters (prefix, blob type, tier) and actions (tier, delete, snapshot delete). Runs once a day; changes can take 24–48 h. |
| Versioning | Automatic previous versions on every write. Combined with **soft delete** for recovery; both are needed for ransomware protection. |
| Soft delete | Blob 1–365 days, container 7–365 days, file share 1–365 days. |
| Immutable storage | **Time-based retention** (WORM) or **legal hold**. A locked policy cannot be removed or shortened. |
| Object replication | Asynchronous copy **blob to blob** between accounts; needs versioning on both, change feed on the source, and no immutability on the destination. |
| Access levels | Private (default) · Blob (anonymous read of blobs) · Container (anonymous list). Container access is usually blocked by policy. |

## Tooling

| Tool | Use it for |
|---|---|
| **AzCopy** | Scripted, high-throughput copy. `azcopy copy`, `azcopy sync`, `--recursive`, SAS or Entra auth. No moves between tiers, but copies and then deletes with `--delete-destination`. |
| **Storage Explorer** | Desktop GUI; connects with Entra, SAS, key, or **connection string**. Can attach to emulator and to Data Lake. |
| **Azure Storage Mover** | Managed migration service for large on-premises file and blob estates. |
| **az storage / AzStorage** | Control-plane and data-plane commands; `az storage blob upload-batch`, `Get-AzStorageBlob`. |
