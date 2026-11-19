# AZ-104 cheat sheet 4/5 — Implement and manage virtual networking

*One page. Facts snapshot 2026-11; verify current behaviour on Microsoft Learn before the exam.*

## VNets and subnets

| Item | What to remember |
|---|---|
| Address space | RFC 1918 private ranges; can add several. Must not overlap any peered or connected network. |
| Reserved addresses | **5 per subnet**: network, first host (gateway), two for Azure DNS, broadcast. A /24 has 251 usable. |
| Smallest subnet | /29 (3 usable). GatewaySubnet should be **/27 or larger**; AzureBastionSubnet needs **/26 or larger**. |
| Reserved names | `GatewaySubnet`, `AzureFirewallSubnet`, `AzureBastionSubnet`, `AzureFirewallManagementSubnet`. |
| Delegation | Hands a subnet to a service (App Service, ACI, SQL Managed Instance). One delegation per subnet; no mixed workloads. |
| Adding a subnet | Allowed while VMs are running. Changing the address space is allowed only if nothing is using the new range. |

## Peering and routing

| Item | What to remember |
|---|---|
| Peering | Private, low-latency, both directions must be created; status must be **Connected** on both sides. Works across regions too, billed as egress. Fails if address spaces **overlap**. |
| Non-transitivity | Peering is **not** transitive: A↔B and B↔C does not connect A to C. Use a hub with a firewall or NVA, or Azure Virtual WAN. |
| Gateway transit | `Allow gateway transit` on the hub peering + `Use remote gateways` on the spoke. One gateway serves many spokes. |
| Service chaining | A UDR in a spoke pointing 0.0.0.0/0 at a firewall in the hub gives inspection and transitive reachability. |
| UDR next hops | Virtual network gateway · Virtual network · Internet · Virtual appliance · **None** (blackhole). |
| Route selection | Longest prefix match first, then **UDR over BGP over system route**. |
| IP forwarding | An NVA NIC must have IP forwarding enabled, or packets are dropped. |
| Default routes | Azure adds system routes for the VNet, peered VNets, and 0.0.0.0/0 → Internet. A UDR overrides the Internet route. |

## IP addressing

| Item | What to remember |
|---|---|
| Public IP SKUs | **Basic** (open by default, no zones, retiring) vs **Standard** (secure by default, static, zone-redundant or zonal, needs an NSG to allow inbound). |
| Allocation | Static or dynamic. Standard public IPs are always static. |
| Private IP | Dynamic or static per NIC. Changing it requires the NIC to be idle. |

## NSGs and ASGs

| Item | What to remember |
|---|---|
| Scope | Subnet and/or NIC. Both are evaluated: **inbound** subnet then NIC, **outbound** NIC then subnet. |
| Default rules | Allow VNet inbound/outbound, allow Azure Load Balancer, deny all other inbound, allow Internet outbound. Cannot be deleted, only overridden by a higher-priority rule. |
| Priority | 100–4096, lower number wins. First match applies; no "deny all" needed unless you want one. |
| Stateful | Return traffic for an allowed flow is automatically allowed. |
| ASG | Groups NICs by role (web, app, db). Rules then reference the ASG as source or destination instead of IP ranges. |
| Service tags | `VirtualNetwork`, `AzureLoadBalancer`, `Internet`, `Storage`, `Sql`, `AzureCloud`, plus service-specific tags. |
| Effective rules | **Network Watcher → Effective security rules** shows the merged result for a NIC. |

## Connectivity services

| Item | What to remember |
|---|---|
| Azure Bastion | PaaS jump host in `AzureBastionSubnet` (/26+). RDP/SSH over TLS 443 in the browser; VMs need **no public IP**. |
| Service endpoint | Extends the VNet identity to a PaaS service over the Azure backbone. Still uses the service's public IP; add the subnet to the service firewall. |
| Private endpoint | A private IP in your subnet for a PaaS service. Disable public access to force it. Needs a **private DNS zone** to resolve. |
| Private Link DNS | `privatelink.blob.core.windows.net` etc., linked to the VNet. Without the link, clients resolve the public IP. |
| VPN Gateway | Site-to-site, point-to-site, or VNet-to-VNet. `GatewaySubnet`, a gateway SKU (VpnGw1+ for production), and a local network gateway for each site. |
| Active-active | Two gateway instances and two tunnels; needs `--enable-active-active` and BGP for automatic failover. |
| ExpressRoute | Private dedicated circuit; needs a gateway with the right SKU and a route filter. Does not traverse the internet. |
| Virtual WAN | Managed hub-and-spoke with transitive routing, VPN, ExpressRoute, and firewall as a service. |

## Name resolution

| Item | What to remember |
|---|---|
| Azure DNS | Public zones. Delegate with an NS record from the registrar. Alias records can point at a public IP, Traffic Manager, or Front Door and follow IP changes. |
| CNAME at apex | Not allowed. Use an **alias A/AAAA** record instead. |
| Private DNS | `*.internal` style zones linked to VNets. `autoregistration` records VM names automatically. |
| Resolution order | Private zone (linked VNet) → Azure-provided DNS → custom DNS servers on the VNet. |
| Cross-VNet | A private zone must be **linked** to every VNet that must resolve it. Peering alone is not enough. |

## Load balancing and delivery

| Item | What to remember |
|---|---|
| Load Balancer SKU | **Basic** (open, no SLA, no zones) vs **Standard** (secure by default, SLA 99.99%, zones). Standard adds **HA ports** rules (all ports and protocols) for NVAs and clustered appliances. |
| Public vs internal | Public LB has a public frontend IP; internal LB uses a private IP in a subnet. Both route by 5-tuple hash. |
| Health probe | TCP, HTTP, or HTTPS. Failed probes remove the instance; there is no automatic re-add without a passing probe. |
| Session persistence | None (5-tuple) · Client IP (2-tuple) · Client IP and protocol (3-tuple). |
| Outbound rules | Standard LB provides outbound SNAT; without a rule or NAT gateway, VMs have no outbound internet. |
| Application Gateway | Layer 7 reverse proxy: path-based routing, TLS termination, cookie affinity, autoscaling (v2), and **WAF** (OWASP CRS; Detection vs Prevention — tune exclusions before prevention). Needs a dedicated subnet. |
| Front Door | Global anycast HTTP(S) entry: latency routing, priority failover, caching, WAF at the edge. Backends can be App Service, App Gateway, or any public endpoint. |

## Troubleshooting

| Tool | Use it for |
|---|---|
| **IP flow verify** | Is this 5-tuple allowed or denied, and which NSG rule decided? |
| **Next hop** | Where would traffic from this VM to that IP go? Shows the effective route type. |
| **Connection troubleshoot** | End-to-end reachability test between a VM and an endpoint, with latency and hops. |
| **Effective routes** | Merged system, BGP, and UDR routes for a NIC. |
| **Connection Monitor** | Continuous, alerting reachability tests across VMs and endpoints. |
| **Packet capture** | Capture at the VM NIC (or via NSG flow logs) for deep analysis. |
