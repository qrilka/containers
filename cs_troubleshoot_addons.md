---

copyright:
  years: 2014, 2021
lastupdated: "2021-05-17"

keywords: kubernetes, iks, help

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
  
 


# Managed add-ons
{: #cs_troubleshoot_addons}

As you use {{site.data.keyword.containerlong}}, consider these techniques for troubleshooting [managed add-ons](/docs/containers?topic=containers-managed-addons), such as Istio.
{: shortdesc}

**Infrastructure provider**:
  * <img src="images/icon-classic.png" alt="Classic infrastructure provider icon" width="15" style="width:15px; border-style: none"/> Classic
  * <img src="images/icon-vpc.png" alt="VPC infrastructure provider icon" width="15" style="width:15px; border-style: none"/> VPC

## Reviewing add-on states and statuses
{: #debug_addons}

You can check the health state and status of a cluster add-on by running the following command:
```
ibmcloud ks cluster addon ls -c <cluster_name_or_ID>
```
{: pre}

Example output:
```
Name            Version   Health State   Health Status
debug-tool      2.0.0     normal         Addon Ready
istio           1.4       -              Enabling
kube-terminal   1.0.0     normal         Addon Ready
```
{: screen}

The **Health State** reflects the lifecycle of the add-on components. The **Health Status** provides details of what add-on operation is in progress. Each state and status is described in the following tables.

|Add-on health state|Description|
|--- |--- |
|`critical`|The add-on is not ready to be used for one of the following reasons:<ul><li>Some or all of the add-on components are unhealthy.</li><li>The add-on failed to deploy.</li><li>The add-on may be at an unsupported version.</li></ul>Check the **Health Status** field for more information.|
|`normal`|The add-on is successfully deployed. Check the status to verify that the add-on is `Ready` or to see if an update is available.|
|`pending`|Some or all components of the add-on are currently deploying. Wait for the state to become `normal` before working with your add-on.|
|`updating`|The add-on is updating and is not ready to be used. Check the **Health Status** field for the version that the add-on is updating to.|
|`warning`|The add-on might not function properly due to cluster limitations. Check the **Health Status** field for more information.|
{: caption="Add-on health states"}
{: summary="Table rows read from left to right, with the add-on state in column one and a description in column two."}

</br>

|Status code|Add-on health status|Description|
|--- |--- |--- |
|H1500|`Addon Ready`|The add-on is successfully deployed and is healthy.|
|H1501, H1502, H1503|`Addon Not Ready`|Some or all of the add-on components are unhealthy. Check whether all add-on component pods are running. For example, for the Istio add-on, check whether all pods in the `istio-system` namespace are `Running` by running `kubectl get pods -n istio-system`.<br><br><img src="images/icon-satellite.svg" alt="{{site.data.keyword.satelliteshort}} infrastructure provider icon" width="15" style="width:15px; border-style: none"/> **{{site.data.keyword.satelliteshort}} clusters**: Also see [Why doesn't my cluster add-on work?](/docs/satellite?topic=satellite-addon-errors).|
|H1504, H1505, H1506, H1507, H1508|`Failure determining health status.`|The add-on health cannot be determined. [Open a support case](/docs/get-support?topic=get-support-using-avatar). In the description, include the error code from the health status.|
|H1509|`Addon Unsupported`|The add-on runs an unsupported version, or the add-on version is unsupported for your cluster version. [Update your add-on to the latest version](/docs/containers?topic=containers-managed-addons#updating-managed-add-ons), or see specific update steps for [Istio](/docs/containers?topic=containers-istio#istio_update).|
|H1510|`Cluster resources low, not enough workers in Ready state.`|The add-on is not ready to be used for one of the following reasons:<ul><li>The cluster does not meet the size criteria for the add-on. For example, check the size requirements for [Istio](/docs/containers?topic=containers-istio#istio_install).</li><li>Worker nodes in your cluster are not in a `Normal` state. [Review the worker nodes' state and status](/docs/containers?topic=containers-debug_worker_nodes).</li></ul>|
|-|`Enabling`|The add-on is currently deploying to the cluster. Note that the add-on might take up to 15 minutes to install.|
|H1512|`Addon daemonset may not be available on all Ready nodes.`|For the static route add-on: The static route operator `DaemonSet` is not available on any worker nodes, which prevents you from applying static route resources. Your worker nodes cannot run the static route operator `DaemonSet` for the following reasons:<ul><li>One or more worker nodes reached their [resource limits](/docs/containers?topic=containers-debug_worker_nodes).</li><li>One or more worker nodes are running the [maximum number of pods per worker node](/docs/containers?topic=containers-limitations#classic_limits).</li></ul>|
{: caption="Add-on health statuses"}
{: summary="Table rows read from left to right, with the add-on status in column one and a description in column two."}

<br />

## Debugging Istio
{: #istio_debug_tool}

To further troubleshoot the [managed Istio add-on](/docs/containers?topic=containers-istio), consider the following debugging tools.
{: shortdesc}

1. Ensure that your Istio components all run the same verion of managed Istio. Whenever the Istio add-on is updated to a new patch or minor version, the Istio control plane is automatically updated, but you must [manually update your data plane components](/docs/containers?topic=containers-istio#update_client_sidecar), including the `istioctl` client and the Istio sidecars for your app.

2. Check your Istio configurations by using the `istioctl analyze` CLI command. For more information about the command, including available command optons and examples, see the [Istio open-source documentation](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-analyze){: external}.

3. Use the {{site.data.keyword.containerlong_notm}} Diagnostics and Debug Tool to run Istio tests and gather pertinent information about the Istio add-on in your cluster. To use the debug tool, you can enable the add-on in your cluster.
    1. In your [cluster dashboard](https://cloud.ibm.com/kubernetes/clusters){: external}, click the name of the cluster where you want to install the debug tool add-on.
    2. Click the **Add-ons** tab.
    3. On the Diagnostics and Debug Tool card, click **Install**.
    4. In the dialog box, click **Install**. Note that it can take a few minutes for the add-on to be installed.
    5. On the Diagnostics and Debug Tool card, click **Dashboard**.
    5. In the debug tool dashboard, select the **istio_control_plane** or **istio_resources**  group of tests. Some tests check for potential warnings, errors, or issues, and some tests only gather information that you can reference while you troubleshoot. For more information about the function of each test, click the information icon next to the test's name.
    6. Click **Run**.
    7. Check the results of each test. If any test fails, click the information icon next to the test's name in the left-hand column for information about how to resolve the issue.

<br />

## Istio components are missing
{: #control_plane}

{: tsSymptoms}
One or more of the Istio control plane components, such as `istiod`, does not exist in your cluster.

{: tsCauses}
* You deleted one of the Istio deployments that is installed in your cluster Istio managed add-on.
* You changed the default `IstioOperator` resource. When you enable the managed Istio add-on, you cannot use `IstioOperator` (`iop`) resources to customize the Istio control plane installation. Only the `IstioOperator` resources that are managed by IBM for the Istio control plane are supported. Changing the control plane settings might result in an unsupported control plane state. If you create an `IstioOperator` resource for custom gateways in your Istio data plane, you are responsible for managing those resources.

{: tsResolve}
To verify the control plane components installation:

1. Check the values of any customizations that you specified in the customization configmap. For example, if the value of the `istio-components-pilot-requests-cpu` setting is too high, control plane components might not be scheduled.
  ```
  kubectl describe cm managed-istio-custom -n ibm-operators
  ```sh
  {: pre}

2. Check the logs of the Istio operator. If any logs indicate that the operator is failing to reconcile your customization settings, verify your settings for the [customized Istio installation](/docs/containers?topic=containers-istio#customize) again.
  ```
  kubectl logs -n ibm-operators -l name=managed-istio-operator
  ```
  {: pre}

3. Optional: To refresh your `managed-istio-custom` configmap resource, delete the configmap from your cluster. After about 5 minutes, a default configmap that contains the original installation settings is created in your cluster. The Istio operator reconciles the installation of Istio to the original add-on settings, including the core components of the Istio control plane.
  ```sh
  kubectl delete cm managed-istio-custom -n ibm-operators
  ```
  {: pre}

4. If Istio control plane components or other Istio resources are still unavailable, refresh the cluster master.
  ```sh
  ibmcloud ks cluster master refresh -c <cluster_name_or_ID>
  ```
  {: pre}
