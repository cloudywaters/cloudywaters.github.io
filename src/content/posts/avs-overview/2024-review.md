---
title: 'Escaping the Data Centre (Without Losing Your Sanity): A Practical Look at Azure VMware Solution'
published: 2025-11-10
description: 'We’ve all been there — the great data centre escape. The racks are humming, the lease is ending, and someone’s just realised that “move to the cloud” isn’t as simple as dragging and dropping a few VMs into Azure.'
image: 'avs-overview.jpeg'
tags: [Azure, AVS, VMWare]
category: 'Tech'
draft: false 
lang: ''
---

We’ve all been there — the great data centre escape. The racks are humming, the lease is ending, and someone’s just realised that “move to the cloud” isn’t as simple as dragging and dropping a few VMs into Azure.  

That’s where Azure VMware Solution (AVS) steps in — the fast lane for getting out of the data centre while keeping the lights on. It’s familiar, it’s VMware, and it buys you time to modernise properly.  

AVS lets you lift and shift workloads into Azure almost unchanged — same vSphere, same vCenter, same NSX, same comfort zone. It’s a great short-term play in a larger cloud transformation, but let’s be clear: it’s not cheap. You’re paying for premium infrastructure and integration, so design and intent matter more than ever.  


## Cluster Design: Contained, Consistent, and Definitely Not Mix-and-Match

Each AVS deployment is its own self-contained environment — you can’t stretch a single cluster across multiple regions or availability zones. If you need redundancy or separation, that means spinning up additional AVS private clouds, each in its own region or zone. Think of them as separate fortresses: connected if you build the bridges, but never sharing walls.  

Inside one AVS private cloud, clusters start at a minimum of three hosts — and these aren’t your average whiteboxes. They’re high-performance, dedicated Azure nodes. You can scale up to 96 hosts per private cloud, but every host must live in the same region and the same availability zone.  

The first cluster in your AVS private cloud isn’t just for your workloads — it also houses the managed VMware infrastructure that Microsoft maintains on your behalf. This includes components like vCenter Server, NSX-T Manager, HCX, and related management appliances. While you don’t manage these directly, they do consume a small portion of your overall capacity, so you’ll need to factor that into your initial sizing.  

No half hosts. No cross-zone clusters. No sneaking in a few leftover workloads “just because.”  

Capacity planning becomes critical here:  
- Over-provision, and you’ll burn through budget faster than you can say “reserved instance.”  
- Under-provision, and you’ll be chasing approvals for more hosts when demand spikes.  

Get the sizing right — including headroom for management infrastructure and future growth — and AVS rewards you with predictable performance and costs from day one.  


## Storage: The Disk Dilemma

By default, AVS uses vSAN storage built across your hosts — fast, integrated, and resilient. But vSAN scales with compute, not independently. Need more storage but don’t want more hosts? That’s where external storage comes in.  

You can’t attach arbitrary Azure disks or random storage accounts to AVS. To scale storage independently, you’ll need to use external storage options such as Azure NetApp Files (ANF) or Azure Elastic SAN. These services integrate well with AVS — but they must be deployed in a dedicated virtual network that’s connected to AVS via ExpressRoute Global Reach or Virtual WAN, and configured with a Premium ExpressRoute Gateway that has FastPath enabled.  

This setup ensures low latency and high throughput, allowing your AVS workloads to access external storage with near-native performance. Skipping this step often results in unexpected bottlenecks or poor IO performance.  

So while vSAN handles your day-to-day storage elegantly, when you step into the external storage world, network design becomes part of the storage design.  

Key takeaways:  
- Keep storage in the same region as your AVS private cloud.  
- Use a Premium ER Gateway with FastPath to reduce latency.  
- Separate storage traffic from management and workload paths for cleaner performance and easier troubleshooting.  

Treat storage like a design domain, not an afterthought — because in AVS, the network is part of your storage stack.  

## Networking: The ExpressRoute Maze

Networking in AVS is where most architects reach for coffee. AVS controls its own NSX-T Tier-0 gateway, which means you don’t get full control over routing. You can’t stretch on-prem subnets into AVS or manually rewire Tier-0 routing.  

AVS connects to your Azure environment (and on-prem) via ExpressRoute. You’ll often need Global Reach or Virtual WAN integration to make traffic flow cleanly. And yes — if you get it wrong, asymmetric routing will turn your elegant migration into an expensive debugging session.  

### Firewalling and Segmentation

Firewalling in AVS deserves its own whiteboard session. You can’t just rely on Azure Firewall sitting in your Virtual WAN hub to handle internal AVS segmentation — traffic between AVS networks doesn’t naturally flow out to Azure for filtering without additional routing gymnastics (and latency you don’t want).  

If you need east–west segmentation inside AVS, your options are:  

- NSX Distributed Firewall (DFW): Built into AVS and ideal for microsegmentation. It gives you per-VM policy control with strong performance — but it’s a premium feature. NSX licensing costs and operational complexity need to be factored in early.  
- Third-party firewalls (for example, Palo Alto, Fortinet, Check Point): You can deploy virtual firewalls inside AVS, but be aware of integration and functionality limitations. Service insertion models are restricted, throughput can be capped, and troubleshooting becomes multi-vendor sport.  
- Azure Firewall or NVA in Azure: Still useful for north–south traffic control (traffic between AVS, Azure native, and on-prem), but not suited for internal AVS segmentation or intra-cluster isolation.  

The takeaway: design your firewalling strategy intentionally. NSX provides the deepest integration, third-party options can fill gaps but come with trade-offs, and Azure Firewall remains best positioned at the cloud edge. Don’t assume one tool will cover all use cases — they’re complementary, not interchangeable.  

Design with intent:  
- Keep routing simple.  
- Document every BGP relationship.  
- Define where each firewall’s responsibility begins and ends.  
- Test failover early, before production depends on it.  

It’s a solid setup when done right — but it’s not forgiving if you wing it.  

## High Availability: Because Things Still Break in the Cloud

Within a single AVS deployment, you’ve got two main HA strategies: stretched clusters or multiple clusters within that same private cloud. Each has trade-offs — and neither lets you mix and match across deployments.  

- Stretched Clusters  
  Stretched clusters let you span two availability zones within the same region. You get synchronous replication with vSAN and automatic failover — ideal for mission-critical workloads that can’t go down. The downside? Cost. You’ll need extra hosts in each zone, and external storage like Azure NetApp Files isn’t supported in this setup.  

- Multiple AVS Deployments for DR  
  If you need disaster recovery across regions — for example, Australia East (AUE) to Australia Southeast (AUSE) — you’ll deploy a separate AVS private cloud in the target region. Each runs independently, so you’ll need a replication tool such as VMware Site Recovery Manager (SRM), Zerto, or Veeam to handle replication and orchestration.  

  Keep in mind: each AVS environment has its own NSX-T Tier-0, so you’ll need to plan ExpressRoute or Global Reach connectivity carefully to avoid routing chaos.  

In short — stretched clusters provide in-region HA, and separate AVS deployments enable regional DR. Both work well, but both come with cost and design complexity that need to be factored in early.  


## Operational Considerations: The Cloud Still Needs Maintenance (Sorry)

Moving to AVS doesn’t magically eliminate operations. It just changes the flavour.  

- Patching and Lifecycle: Microsoft manages the hardware and ESXi layer, but you still manage your VMs, vCenter, and NSX configurations. Think of it like renting a serviced apartment — they fix the plumbing, you still do the dishes.  
- Backups and DR: AVS doesn’t come with built-in backups. You’ll need something like Veeam, Commvault, or Azure Backup Server. For DR, pair AVS with SRM or a secondary AVS cloud.  
- Monitoring: AVS integrates neatly with Azure Monitor, Log Analytics, and VM Insights — but native Azure policies and tags don’t automatically apply inside your VMware bubble.  
- Automation: Tools like Terraform, PowerCLI, and vRealize (Aria) still work — just remember you’re talking to a managed vCenter, not your own. Be gentle.  

### Extending Governance with Azure Arc

If you want to bridge the operational and governance gap between AVS and native Azure services, Azure Arc is your new best friend.  

- Azure Arc for VMware: Allows you to onboard your vSphere-based VMs running in AVS into Azure, giving you a single management plane for inventory, policy, tagging, and compliance. You can even deploy extensions such as Azure Monitor Agent (AMA) or Defender for Servers across your VMware workloads directly from the Azure Portal.  
- Azure Arc for Servers: Extends this even further by onboarding non-VMware or hybrid workloads — physical servers, other hypervisors, or multi-cloud VMs — under the same Azure Resource Manager control plane.  

Together, these tools let you apply Azure Policy, Security Center, Update Management, and tagging across your hybrid fleet — giving you that “single pane of glass” view that architects have been chasing since the dawn of dashboards.  


## When AVS Makes Sense (and When It Really Doesn’t)

AVS is brilliant when used with intent. It’s a bridge — not a destination.  

Use it when:  
- You’ve got a data centre lease expiring and need to migrate quickly.  
- You have complex legacy workloads that aren’t cloud-ready.  
- Your team is VMware-savvy and you want to leverage those skills while modernising around Azure.  
- You need a robust DR target for your on-prem VMware environment. 
- You are a large scale enterprise 

Avoid it when:  
- You’re already running modern, cloud-native workloads.  
- Budget is your number-one constraint.  
- You need elastic, per-minute scaling.  
- You only have a small footprint required  

AVS is premium infrastructure for very specific scenarios. When used right, it’s worth every cent. When used wrong, it’s a very expensive hotel stay for your old workloads.  

## Wrapping Up: The Right Bridge to the Cloud

Azure VMware Solution isn’t a silver bullet, but it’s one of the best bridges available for getting out of the data centre fast — without rearchitecting everything overnight.  

The key is awareness: understand what AVS does brilliantly, what it limits, and what it costs. Plan your clusters, networking, storage, firewalling, and HA design deliberately. And don’t forget to budget for the operational side — because the cloud might be managed, but it’s never maintenance-free.  

Used wisely, AVS gives you breathing space — a chance to modernise at your own pace, without the panic of a hard cutover.  

And honestly? That’s worth a lot.  
