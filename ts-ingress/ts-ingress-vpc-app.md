---

copyright:
  years: 2014, 2021
lastupdated: "2021-05-19"

keywords: kubernetes, iks, nginx, nlb, help

subcollection: containers
content-type: troubleshoot

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


# VPC clusters: Why can't my app connect via Ingress?
{: #vpc_ts_alb}

**Infrastructure provider**:
* <img src="../images/icon-vpc.png" alt="VPC infrastructure provider icon" width="15" style="width:15px; border-style: none"/> VPC

{: tsSymptoms}
You exposed your app by creating an Ingress resource for your app in your VPC cluster. When you tried to connect to your app by using the subdomain of the Ingress application load balancer (ALB), the connection failed or timed out.

{: tsCauses}
When you create a VPC cluster, one public and one private VPC load balancer are automatically created outside of your cluster in your VPC. The VPC load balancer routes requests to the apps that the ALBs expose. Requests cannot be routed to your app in the following situations:
* A VPC security group is blocking incoming traffic to your worker nodes, including incoming requests to your app.
* The VPC load balancer is offline, such as due to load balancer provisioning errors or VSI connection errors.
* The VPC load balancer is deleted through the VPC console or the CLI.
* The VPC load balancer's DNS entry is still registering.

{: tsResolve}
Verify that no VPC security groups are blocking traffic to your cluster and that the VPC load balancer is available.
1. Install the `infrastructure-service` plug-in. The prefix for running commands is `ibmcloud is`.
  ```
  ibmcloud plugin install infrastructure-service
  ```
  {: pre}

2. Kubernetes version 1.18 or earlier only: [Allow traffic requests that are routed by the VPC load balancer to node ports on your worker nodes](/docs/containers?topic=containers-vpc-network-policy#security_groups).

3. Verify that the VPC load balancer for your ALBs exists. In the output, look for the VPC load balancer **Name** that starts with `kube-crtmgr-<cluster_ID>`. If you did not install the `infrastructure-service` plug-in, install it by running `ibmcloud plugin install infrastructure-service`.
  ```
  ibmcloud is load-balancers
  ```
  {: pre}
  * If the VPC load balancer is not listed, it was deleted through the VPC console or the CLI. To re-create the VPC load balancer for your ALBs, disable all of the public or private ALBs that are assigned that VPC load balancer's hostname by running `ibmcloud ks ingress alb disable vpc-gen2 --alb <ALB_ID> -c <cluster_name_or_ID>` for each ALB. Then, re-enable those ALBs by running `ibmcloud ks ingress alb enable vpc-gen2 --alb <ALB_ID> -c <cluster_name_or_ID>` for each ALB. A new VPC load balancer for the ALBs takes a few minutes to provision in your VPC. You cannot access your app until the VPC load balancer for your ALBs is fully provisioned.
  * If the VPC load balancer is listed, its DNS entry might still be registering. When a VPC load balancer is created, the hostname is registered through a public DNS. In some cases, it can take several minutes for this DNS entry to be replicated to the specific DNS that your client is using. You can either wait for the hostname to be registered in your DNS, or access the VPC load balancer directly by using one of its IP addresses. To find the VPC load balancer IP addresses, run `ibmcloud is lb <LB_ID>` and look for the **Public IPs** field. If after several minutes you cannot reach the load balancer, it might be offline due to provisioning or connection issues. [Open an {{site.data.keyword.cloud_notm}} support case](https://cloud.ibm.com/unifiedsupport/cases/add). For the type, select **Technical**. For the category, select **Network** in the VPC section. In the description, include your cluster ID and the VPC load balancer ID.
