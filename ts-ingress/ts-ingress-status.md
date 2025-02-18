---

copyright:
  years: 2014, 2021
lastupdated: "2021-05-19"

keywords: kubernetes, iks, help, network, connectivity

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
  
  

# Checking the status of Ingress components
{: #ingress-status}

**Infrastructure provider**:
* <img src="../images/icon-classic.png" alt="Classic infrastructure provider icon" width="15" style="width:15px; border-style: none"/> Classic
* <img src="../images/icon-vpc.png" alt="VPC infrastructure provider icon" width="15" style="width:15px; border-style: none"/> VPC

## Getting the status and message
{: #check_status}

To check the overall health and status of your cluster's Ingress components:
{: shortdesc}

```
ibmcloud ks ingress status -c <cluster_name_or_ID>
```
{: pre}

The state of the Ingress components are reported in an **Ingress Status** and **Ingress Message**. Example output:
```
Ingress Status:   healthy
Message:          All Ingress components are healthy

Component                                        Status    Type
public-crdf253b6025d64944ab99ed63bb4567b6-alb1   healthy   alb
public-crdf253b6025d64944ab99ed63bb4567b6-alb2   healthy   alb
```
{: screen}

The **Ingress Status** and **Ingress Message** fields are also returned in the output of the `ibmcloud ks cluster get` command. The health of your Ingress components might impact the health of your cluster master. For example,  if your Ingress components are unhealthy, your cluster master might show a `warning` state. However, the health of your Ingress components does not cause your master health to become `critical`.
{: tip}

## Ingress statuses
{: #ingress_status}

The Ingress Status reflects the overall health of the Ingress components.
{: shortdesc}

|Ingress status|Description|
|--- |--- |
|`healthy` |The Ingress components are healthy. Check the **Ingress Message** field to verify that all operations for the Ingress components are complete.|
|`warning` |The Ingress components might not function properly due to errors. Check the **Ingress Message** field for more information and troubleshooting.|
{: caption="Ingress statuses"}
{: summary="Table rows read from left to right, with the Ingress status in column one and a description in column two."}

## Ingress messages
{: #ingress_message}

The Ingress Message provides details of what operation is in progress or information about any components that are unhealthy. Each status and message is described in the following tables.
{: shortdesc}

|Ingress message|Description|
|--- |--- |
|`ALB is disabled` |Your public ALBs were manually disabled. For more information, see the [`ibmcloud ks ingress alb enable` CLI command reference](/docs/containers?topic=containers-cli-plugin-kubernetes-service-cli#cs_alb_configure).|
|`ALB is unhealthy or unreachable` |One or more ALB IP addresses cannot be reached. For troubleshooting information, see [Ping the ALB subdomain and public IP addresses](/docs/containers?topic=containers-ingress-debug#ping).|
|`ALBs are not health checked in clusters created with no subnets` |Ingress health reporting is not supported for classic clusters that were created with the `--no-subnet` flag.|
|`ALBs are not health checked in private-only clusters` |Ingress health reporting is not supported for classic clusters that are connected to private VLANs only.|
|`ALBs cannot be created because no portable subnet is available` |1Each ALB is created with a portable public or private IP address from the public or private subnet on the VLANs that your classic cluster is connected to. If no portable IP address is available, the ALB is not created. You might need to add a new subnet to your cluster or order a new VLAN. For troubleshooting information, see [Classic clusters: Why does the ALB not deploy in a zone?](/docs/containers?topic=containers-cs_subnet_limit).|
|`All Ingress components are healthy` |The Ingress components are successfully deployed and are healthy.|
|`Creating Ingress ALBs` |Your ALBs are currently deploying. Wait until your ALBs are fully deployed to review the health of your Ingress components. Note that ALB creation can take up to 15 minutes to complete. |
|`Creating TLS certificate for Ingress subdomain, which might take several minutes. Ensure you have the correct IAM permissions.` |The default Ingress subdomain for your cluster is created with a default TLS certificate, which is stored in the **Ingress Secret**. The certificate is currently being created and stored in the default {{site.data.keyword.cloudcerts_long_notm}} instance for your cluster.<p class="note">When the creation of the {{site.data.keyword.cloudcerts_short}} instance is triggered, the {{site.data.keyword.cloudcerts_short}} instance might take up to an hour to become visible in the {{site.data.keyword.cloud_notm}} console. If this message continues to be displayed, see [Why does no Ingress secret exist after cluster creation?](/docs/containers?topic=containers-ingress_secret).</p>|
|`Could not create a Certificate Manager instance. Ensure you have the correct IAM platform permissions.` | A default {{site.data.keyword.cloudcerts_long_notm}} instance for your cluster was not created to store the TLS certificate for the Ingress subdomain. The API key for the resource group and region that your cluster is in does not have the correct IAM permissions for {{site.data.keyword.cloudcerts_short}}. For troubleshooting steps, see [Why does no Ingress secret exist after cluster creation?](/docs/containers?topic=containers-ingress_secret).|
|`Could not upload certificates to Certificate Manager instance. Ensure you have the correct IAM service permissions.` |The TLS certificate for your cluster's default Ingress subdomain is created, but cannot be stored in the default {{site.data.keyword.cloudcerts_long_notm}} instance for your cluster. The API key for the resource group and region that your cluster is in does not have the correct IAM permissions for {{site.data.keyword.cloudcerts_short}}. For troubleshooting steps, see [Why does no Ingress secret exist after cluster creation?](/docs/containers?topic=containers-ingress_secret).|
|`Ingress is not supported for free clusters` |In a free cluster, you can expose your app only by using a [NodePort service](/docs/containers?topic=containers-nodeport).|
|`Ingress subdomain is unreachable` |Your cluster is assigned an Ingress subdomain in the format `<cluster_name>.<region>.containers.mybluemix.net` that cannot be reached. For troubleshooting information, see [Ping the ALB subdomain and public IP addresses](/docs/containers?topic=containers-ingress-debug#ping).|
|`Load balancer service for ALB or router is not ready` |<ul><li>VPC clusters: The VPC load balancer that routes requests to the apps that your ALBs expose either might still be creating or did not correctly deploy to your VPC. For troubleshooting information, see [VPC clusters: Why can't my app connect via Ingress?](/docs/containers?topic=containers-vpc_ts_alb).</li><li>Classic clusters: The load balancer service that exposes your ALB did not correctly deploy to your cluster. For troubleshooting information, see [Classic clusters: Why does the ALB not deploy in a zone?](/docs/containers?topic=containers-cs_subnet_limit).</li></ul>|
|`No workers found in this zone` | ALB pods cannot deploy to a zone because no worker nodes match the pod affinity requirements. To ensure that you have the minimum required worker nodes per zone, see [Why do ALB pods not deploy to worker nodes?](/docs/containers?topic=containers-alb-pod-affinity).|
|`One or more ALBs are unhealthy` |The external IP address for one or more of your ALBs was reported as unhealthy. For troubleshooting information, see [Ping the ALB subdomain and public IP addresses](/docs/containers?topic=containers-ingress-debug#ping).|
|`Pending update or enable operation for ALB in progress` |Your ALB is currently updating to a new version, or your ALB that was previously disabled is enabling. For information about updating ALBs, see [Updating ALBs](/docs/containers?topic=containers-ingress#alb-update). For information about enabling ALBs, see the [`ibmcloud ks ingress alb enable` CLI command reference](/docs/containers?topic=containers-cli-plugin-kubernetes-service-cli#cs_alb_configure).|
|`Registering Ingress subdomain` |The default **Ingress Subdomain** for your cluster is currently being created. The Ingress subdomain and secret creation follows a process that might take more than 15 minutes to complete. For troubleshooting information, see [Why does no Ingress subdomain exist after cluster creation?](/docs/openshift?topic=openshift-ingress_subdomain).|
|`The expiration dates reported by Ingress secrets are out of sync across namespaces.` | To resynchronize the expiration dates, [regenerate the secrets for your Ingress subdomain certificate](/docs/containers?topic=containers-sync_cert_dates).|
{: caption="Ingress messages"}
{: summary="Table rows read from left to right, with the Ingress message in column one and a description in column two."}
