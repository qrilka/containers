---

copyright:
  years: 2014, 2021
lastupdated: "2021-05-14"

keywords: kubernetes, iks, multi az, multi-az, szr, mzr

subcollection: containers

---

{:DomainName: data-hd-keyref="APPDomain"}
{:DomainName: data-hd-keyref="DomainName"}
{:android: data-hd-operatingsystem="android"}
{:api: .ph data-hd-interface='api'}
{:apikey: data-credential-placeholder='apikey'}
{:app_key: data-hd-keyref="app_key"}
{:app_name: data-hd-keyref="app_name"}
{:app_secret: data-hd-keyref="app_secret"}
{:app_url: data-hd-keyref="app_url"}
{:authenticated-content: .authenticated-content}
{:beta: .beta}
{:c#: data-hd-programlang="c#"}
{:cli: .ph data-hd-interface='cli'}
{:codeblock: .codeblock}
{:curl: .ph data-hd-programlang='curl'}
{:deprecated: .deprecated}
{:dotnet-standard: .ph data-hd-programlang='dotnet-standard'}
{:download: .download}
{:external: target="_blank" .external}
{:faq: data-hd-content-type='faq'}
{:fuzzybunny: .ph data-hd-programlang='fuzzybunny'}
{:generic: data-hd-operatingsystem="generic"}
{:generic: data-hd-programlang="generic"}
{:gif: data-image-type='gif'}
{:go: .ph data-hd-programlang='go'}
{:help: data-hd-content-type='help'}
{:hide-dashboard: .hide-dashboard}
{:hide-in-docs: .hide-in-docs}
{:important: .important}
{:ios: data-hd-operatingsystem="ios"}
{:java: .ph data-hd-programlang='java'}
{:java: data-hd-programlang="java"}
{:javascript: .ph data-hd-programlang='javascript'}
{:javascript: data-hd-programlang="javascript"}
{:new_window: target="_blank"}
{:note .note}
{:note: .note}
{:objectc data-hd-programlang="objectc"}
{:org_name: data-hd-keyref="org_name"}
{:php: data-hd-programlang="php"}
{:pre: .pre}
{:preview: .preview}
{:python: .ph data-hd-programlang='python'}
{:python: data-hd-programlang="python"}
{:route: data-hd-keyref="route"}
{:row-headers: .row-headers}
{:ruby: .ph data-hd-programlang='ruby'}
{:ruby: data-hd-programlang="ruby"}
{:runtime: architecture="runtime"}
{:runtimeIcon: .runtimeIcon}
{:runtimeIconList: .runtimeIconList}
{:runtimeLink: .runtimeLink}
{:runtimeTitle: .runtimeTitle}
{:screen: .screen}
{:script: data-hd-video='script'}
{:service: architecture="service"}
{:service_instance_name: data-hd-keyref="service_instance_name"}
{:service_name: data-hd-keyref="service_name"}
{:shortdesc: .shortdesc}
{:space_name: data-hd-keyref="space_name"}
{:step: data-tutorial-type='step'}
{:subsection: outputclass="subsection"}
{:support: data-reuse='support'}
{:swift: .ph data-hd-programlang='swift'}
{:swift: data-hd-programlang="swift"}
{:table: .aria-labeledby="caption"}
{:term: .term}
{:tip: .tip}
{:tooling-url: data-tooling-url-placeholder='tooling-url'}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:tsSymptoms: .tsSymptoms}
{:tutorial: data-hd-content-type='tutorial'}
{:ui: .ph data-hd-interface='ui'}
{:unity: .ph data-hd-programlang='unity'}
{:url: data-credential-placeholder='url'}
{:user_ID: data-hd-keyref="user_ID"}
{:vbnet: .ph data-hd-programlang='vb.net'}
{:video: .video}
  
  

# Planning your cluster for high availability
{: #ha_clusters}

Design your standard cluster for maximum availability and capacity for your app with {{site.data.keyword.containerlong}}.
{: shortdesc}

Your users are less likely to experience downtime when you distribute your apps across multiple worker nodes, zones, and clusters. Built-in capabilities, like load balancing and isolation, increase resiliency against potential failures with hosts, networks, or apps. Review these potential cluster setups that are ordered with increasing degrees of availability.

![High availability for clusters](images/cs_cluster_ha_roadmap_multizone_public.png)

1. A [single zone cluster](#single_zone) with multiple worker nodes in a worker pool.
2. A [multizone cluster](#multizone) that spreads worker nodes across zones within one region.
3. **Clusters with public network connectivity**: [Multiple clusters](#multiple_clusters) that are set up across zones or regions and that are connected via a global load balancer.

## Single zone cluster
{: #single_zone}

Single zone clusters can be created in one of the supported [single zone locations](/docs/containers?topic=containers-regions-and-zones#zones-sz). To improve availability for your app and to allow failover for the case that one worker node is not available in your cluster, add additional worker nodes to your single zone cluster.
{: shortdesc}

<img src="images/icon-vpc.png" alt="VPC infrastructure provider icon" width="15" style="width:15px; border-style: none"/> VPC clusters are supported only in [multizone metro locations](/docs/containers?topic=containers-regions-and-zones#zones-vpc). If your cluster must reside in one of the single zone cities, create a classic cluster instead.
{: note}

<img src="images/cs_cluster_singlezone.png" alt="High availability for clusters in a single zone" width="230" style="width:230px; border-style: none"/>

You can add more worker nodes to your cluster by [resizing an existing worker pool](/docs/containers?topic=containers-add_workers#resize_pool) or by [adding a new worker pool](/docs/containers?topic=containers-add_workers#add_pool). When you add more worker nodes, app instances can be distributed across multiple worker nodes. If one worker node goes down, app instances on available worker nodes continue to run. Kubernetes automatically reschedules pods from unavailable worker nodes to ensure performance and capacity for your app. To ensure that your pods are evenly distributed across worker nodes, implement [pod affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/).

**Is my master highly available in a single zone cluster?**

If your cluster is created in a single zone city, the Kubernetes master of your classic cluster is highly available and includes replicas on separate physical hosts for your master API server, etcd, scheduler, and controller manager to protect against an outage such as during a master update. If your cluster resides in one of the multizone metro locations, the master is automatically deployed with three replicas and spread across the zones of the metro.

**How can I protect my workloads against a single zone failure?**

If your single zone cluster is created in one of the [multizone metro locations](/docs/containers?topic=containers-regions-and-zones#zones-mz), you can change your single zone cluster to a [multizone cluster](#multizone). In a multizone cluster, your workloads are distributed across worker nodes in different zones. If one zone is not available, your workloads continue to run in the remaining zones. If you prefer single zone clusters for simplified management, or if your cluster must reside in a specific [single zone city](/docs/containers?topic=containers-regions-and-zones#zones-sz) that does not support multizone capabilities, you can create [multiple clusters](#multiple_clusters) and connect them with a global load balancer.

## Multizone cluster
{: #multizone}

Create a multizone cluster to distribute your workloads across multiple worker nodes and zones, and protect against zone failures with hosts, networks, or apps. If resources in one zone go down, your cluster workloads continue to run in the other zones.
{: shortdesc}

<img src="images/cs_cluster_multizone-ha.png" alt="High availability for multizone clusters" width="500" style="width:500px; border-style: none"/>

In a multizone cluster, the worker nodes in your worker pools are replicated across multiple zones within one region. Multizone clusters are designed to evenly schedule pods across worker nodes and zones to assure availability and failure recovery. If worker nodes are not spread evenly across the zones or capacity is insufficient in one of the zones, the Kubernetes scheduler or {{site.data.keyword.openshiftshort}} controller might fail to schedule all requested pods. As a result, pods might go into a **Pending** state until enough capacity is available. If you want to change the default behavior to make Kubernetes scheduler or {{site.data.keyword.openshiftshort}} controller distribute pods across zones in a best effort distribution, use the `preferredDuringSchedulingIgnoredDuringExecution` [pod affinity policy](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/).

<p class="note">You can create a multizone cluster in one of the supported [classic](/docs/containers?topic=containers-regions-and-zones#zones-mz) or [VPC](/docs/containers?topic=containers-regions-and-zones#zones-vpc) multizone locations only.</p>

**Why do I need worker nodes in three zones?**

Distributing your workload across three zones ensures high availability for your app in case a zone becomes unavailable. You must have your worker nodes spread evenly across all three availability zones to meet the [{{site.data.keyword.cloud_notm}} service level agreement (SLA)](/docs/overview?topic=overview-slas) for HA configuration. This setup also makes your cluster more cost-efficient. Why is that, you ask? Here is an example.

Let's say you need a worker node with six cores to handle the workload for your app. To make your cluster more available, you have the following options:

- **Duplicate your resources in another zone:** This option leaves you with two worker nodes, each with six cores in each zone for a total of 12 cores. </br>
- **Distribute resources across three zones:** With this option, you deploy three cores per zone, which leaves you with a total capacity of nine cores. To handle your workload, two zones must be up at a time. If one zone is unavailable, the other two zones can fully handle your six-core workload. If two zones are unavailable, the three remaining cores are up to handle your parts of your workload, and you could temporarily add another worker node to that zone. Deploying three cores per zone means smaller machines and hence reduced cost for you.</br>

**How is my Kubernetes master set up?**

When you create a cluster in a multizone location, a highly available master is automatically deployed and three replicas are spread across the zones of the metro. For example, if the cluster is in `dal10`, `dal12`, or `dal13` zones, the replicas of the master are spread across each zone in the Dallas multizone metro.

**Do I have to do anything so that the master can communicate with the workers across zones?**

If you created a VPC multizone cluster, the subnets in each zone are automatically set up with Access Control Lists (ACLs) that allow communication between the master and the worker nodes across zones. In classic clusters, if you have multiple VLANs for your cluster, multiple subnets on the same VLAN, or a multizone classic cluster, you must enable a [Virtual Router Function (VRF)](/docs/account?topic=account-vrf-service-endpoint#vrf) for your IBM Cloud infrastructure account so your worker nodes can communicate with each other on the private network. To enable VRF, see [Enabling VRF](/docs/account?topic=account-vrf-service-endpoint#vrf). To check whether a VRF is already enabled, use the `ibmcloud account show` command. If you cannot or do not want to enable VRF, enable [VLAN spanning](/docs/vlans?topic=vlans-vlan-spanning#vlan-spanning). To perform this action, you need the **Network > Manage Network VLAN Spanning** [infrastructure permission](/docs/containers?topic=containers-users#infra_access), or you can request the account owner to enable it. To check whether VLAN spanning is already enabled, use the `ibmcloud ks vlan spanning get --region <region>` [command](/docs/containers?topic=containers-cli-plugin-kubernetes-service-cli#cs_vlan_spanning_get).

**Can I convert my single zone cluster to a multizone cluster?**

To convert a single zone cluster to a multizone cluster, your cluster must be set up in one of the supported [classic](/docs/containers?topic=containers-regions-and-zones#zones-mz) or [VPC](/docs/containers?topic=containers-regions-and-zones#zones-vpc) multizone locations. VPC clusters can be set up only in multizone metro locations, and as such can always be converted from a single zone cluster to a multizone cluster. Classic clusters that are set up in a single zone data center cannot be converted to a multizone cluster. To convert a single zone cluster to a multizone cluster, see [Adding worker nodes by adding a zone to a worker pool](/docs/containers?topic=containers-add_workers#add_zone).



**Do my apps automatically spread across zones?**

It depends on how you set up the app. See [Planning highly available deployments](/docs/containers?topic=containers-plan_deploy#highly_available_apps) and [Planning highly available persistent storage](/docs/containers?topic=containers-storage_planning).

## Multiple public clusters connected with a global load balancer
{: #multiple_clusters}

To protect your app from a master failure or for classic clusters that must reside in one of the supported [single zone locations](/docs/containers?topic=containers-regions-and-zones#zones-sz), you can create multiple clusters in different zones within a region and connect them with a global load balancer.
{: shortdesc}

To connect multiple clusters with a global load balancer, the clusters must be set up with public network connectivity.
{: note}

<img src="images/cs_multiple_cluster_zones.png" alt="High availability for multiple clusters" width="700" style="width:700px; border-style: none"/>

To balance your workload across multiple clusters, you must set up a global load balancer and add the public IP addresses of your ALBs or load balancer services to your domain. By adding these IP addresses, you can route incoming traffic between your clusters. For the global load balancer to detect if one of your clusters is unavailable, consider adding a ping-based health check to every IP address. When you set up this check, your DNS provider regularly pings the IP addresses that you added to your domain. If one IP address becomes unavailable, then traffic is not sent to this IP address anymore. However, Kubernetes does not automatically restart pods from the unavailable cluster on worker nodes in available clusters. If you want Kubernetes to automatically restart pods in available clusters, consider setting up a [multizone cluster](#multizone).

**Why do I need 3 clusters in three zones?**

Similar to using [3 zones in multizone clusters](#multizone), you can provide more availability to your app by setting up three clusters across zones. You can also reduce costs by purchasing smaller machines to handle your workload.

**What if I want to set up multiple clusters across regions?**

You can set up multiple clusters in different regions of one geolocation (such as US South and US East) or across geolocations (such as US South and EU Central). Both setups offer the same level of availability for your app, but also add complexity when it comes to data sharing and data replication. For most cases, staying within the same geolocation is sufficient. But if you have users across the world, it might be better to set up a cluster where your users are so that your users do not experience long waiting times when they send a request to your app.

**What options do I have to load balance workloads across multiple clusters?**

To load balance workloads across multiple clusters, you must make your apps available on the public network by using [Application Load Balancers (ALBs)](/docs/containers?topic=containers-ingress-about) or [Network Load Balancers (NLBs)](/docs/containers?topic=containers-loadbalancer-about). The ALBs and NLBs are assigned a public IP address that you can use to access your apps.

To load balance workloads across your apps, add the public IP addresses of your ALBs and NLBs to a CIS global load balancer or your own global load balancer.

**To use a CIS global load balancer**:
1.  Set up [Kubernetes load balancer services](/docs/containers?topic=containers-loadbalancer-qs) or the [Ingress service](/docs/containers?topic=containers-ingress) to expose the apps in your cluster.
2.  Set up the CIS global load balancer by following steps 1 - 5 in [Getting Started with {{site.data.keyword.cloud_notm}} Internet Services (CIS)](/docs/cis?topic=cis-getting-started#getting-started). These steps walk you through provisioning the service instance, adding your app domain, and configuring your name servers, and creating DNS records. Create a DNS record for each ALB or NLB IP address that you collected. These DNS records map your app domain to all of your cluster ALBs or NLBs, and ensure that requests to your app domain are forwarded to your clusters in a round-robin cycle.
3. [Add health checks](/docs/cis?topic=cis-configure-glb#add-a-health-check) for the ALBs or NLBs. You can use the same health check for the ALBs or NLBs in all of your clusters, or create specific health checks to use for specific clusters.
4. [Add an origin pool](/docs/cis?topic=cis-configure-glb#add-a-pool) for each cluster by adding the cluster's ALB or NLB IPs. For example, if you have 3 clusters that each have two ALBs, create three origin pools that each have two ALB IP addresses. You can find the NLB or ALB IP addresses by running `kubectl get svc -n <namespace>`. Add a health check to each origin pool that you create.
4. [Add a global load balancer](/docs/cis?topic=cis-configure-glb).

**To use your own global load balancer**:
1. Set up [Kubernetes load balancer services](/docs/containers?topic=containers-loadbalancer-qs) or set up the [Ingress service](/docs/containers?topic=containers-ingress) to expose the apps in your cluster.
2. Configure your domain to route incoming traffic to your ALB or NLB services by adding the IP addresses of all public enabled ALBs and NLB services to your domain. You can find the NLB or ALB IP addresses by running `kubectl get svc -n <namespace>`.
3. For each IP address, enable a ping-based health check so that your DNS provider can detect unhealthy IP addresses. If an unhealthy IP address is detected, traffic is not routed to this IP address anymore.

**What if I want to load balance workloads on the private network?**

{{site.data.keyword.cloud_notm}} does not offer a load balancer service on the private network. However, you can connect your cluster to a private load balancer that you host in your on-prem network by using one of the [supported VPN options](/docs/containers?topic=containers-vpn). Make sure to expose your apps on the private network by using [Application Load Balancers (ALBs)](/docs/containers?topic=containers-ingress-about) or [Network Load Balancers (NLBs)](/docs/containers?topic=containers-loadbalancer-about), and use the private IP address in your VPN settings to connect your app to your on-prem network.


