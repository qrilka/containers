---

copyright:
  years: 2014, 2021
lastupdated: "2021-05-14"

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
 


# Debugging persistent storage failures
{: #debug_storage}
{: troubleshoot}
{: support}

Review the options to debug persistent storage and find the root causes for failures.
{: shortdesc}

1. Check whether the pod that mounts your storage instance is successfully deployed.
   1. List the pods in your cluster. A pod is successfully deployed if the pod shows a status of **Running**.
      ```
      kubectl get pods
      ```
      {: pre}

   2. Get the details of your pod and check whether errors are displayed in the **Events** section of your CLI output.
      ```
      kubectl describe pod <pod_name>
      ```
      {: pre}


   3. Retrieve the logs for your app and check whether you can see any error messages.
      ```
      kubectl logs <pod_name>
      ```
      {: pre}

2. Restart your pod. If your pod is part of a deployment, delete the pod and let the deployment rebuild it. If your pod is not part of a deployment, delete the pod and reapply your pod configuration file.
  ```
  kubectl delete pod <pod_name>
  ```
  {: pre}

  ```
  kubectl apply -f <app.yaml>
  ```
  {: pre}

3. If restarting your pod does not resolve the issue, [reload your worker node](/docs/containers?topic=containers-cli-plugin-kubernetes-service-cli#cs_worker_reload).

4. Verify that you use the latest {{site.data.keyword.cloud_notm}} and {{site.data.keyword.containerlong_notm}} plug-in version.
   ```
   ibmcloud update
   ```
   {: pre}

   ```
   ibmcloud plugin repo-plugins
   ```
   {: pre}

5. Verify that the storage driver and plug-in pods show a status of **Running**.
   1. List the pods in the `kube-system` namespace.
      ```
      kubectl get pods -n kube-system
      ```
      {: pre}

   2. If the storage driver and plug-in pods do not show a **Running** status, get more details of the pod to find the root cause. Depending on the status of your pod, you might not be able to execute all of the following commands.
      1. Get the names of the containers that run in the driver pod.
         ```
         kubectl get pod <pod_name> -n kube-system -o jsonpath="{.spec['containers','initContainers'][*].name}" | tr -s '[[:space:]]' '\n'
         ```
         {: pre}

         Example output for {{site.data.keyword.block_storage_is_short}} with three containers:
         ```
         csi-provisioner
         csi-attacher
         iks-vpc-block-driver
         ```
         {: screen}

         Example output for {{site.data.keyword.blockstorageshort}}:
         ```
         ibmcloud-block-storage-driver-container
         ```
         {: pre}

      2. Export the logs from the driver pod to a `logs.txt` file on your local machine. Include the driver container name.

         ```
         kubectl logs <pod_name> -n kube-system -c <container_name> > logs.txt
         ```
         {: pre}

      3. Review the log file.
         ```
         cat logs.txt
         ```
         {: pre}

   3. Analyze the **Events** section of the CLI output of the `kubectl describe pod` command and the latest logs to find the root cause for the error.

6. Check whether your PVC is successfully provisioned.
   1. Check the state of your PVC. A PVC is successfully provisioned if the PVC shows a status of **Bound**.
      ```
      kubectl get pvc
      ```
      {: pre}

   2. If the state of the PVC shows **Pending**, retrieve the error for why the PVC remains pending.
      ```
      kubectl describe pvc <pvc_name>
      ```
      {: pre}

   3. Review common errors that can occur during the PVC creation.
      - [File storage and classic block storage: PVC remains in a pending state](#file_pvc_pending)
      - [Object storage: PVC remains in a pending state](#cos_pvc_pending)

   4. Review common errors that can occur when you mount a PVC to your app.
      - [File storage: App cannot access or write to PVC](#file_app_failures)
      - [Classic Block storage: App cannot access or write to PVC](#block_app_failures)
      - [Object storage: Accessing files with a non-root user fails](#cos_nonroot_access)


7. Verify that the `kubectl` CLI version that you run on your local machine matches the Kubernetes version that is installed in your cluster. If you use a `kubectl` CLI version that does not match at least the major.minor version of your cluster, you might experience unexpected results. For example, [Kubernetes does not support ![External link icon](../icons/launch-glyph.svg “External link icon”)](https://kubernetes.io/releases/version-skew-policy/) `kubectl` client versions that are 2 or more versions apart from the server version (n +/- 2).
   1. Show the `kubectl` CLI version that is installed in your cluster and your local machine.
      ```
      kubectl version
      ```
      {: pre}

      Example output:
      ```
      Client Version: version.Info{Major:"1", Minor:"1.20", GitVersion:"v1.20.6", GitCommit:"641856db18352033a0d96dbc99153fa3b27298e5", GitTreeState:"clean", BuildDate:"2019-03-25T15:53:57Z", GoVersion:"go1.12.1", Compiler:"gc", Platform:"darwin/amd64"}
      Server Version: version.Info{Major:"1", Minor:"1.20", GitVersion:"v1.20.6+IKS", GitCommit:"e15454c2216a73b59e9a059fd2def4e6712a7cf0", GitTreeState:"clean", BuildDate:"2019-04-01T10:08:07Z", GoVersion:"go1.11.5", Compiler:"gc", Platform:"linux/amd64"}
      ```
      {: screen}

      The CLI versions match if you can see the same version in `GitVersion` for the client and the server. You can ignore the `+IKS` part of the version for the server.
   2. If the `kubectl` CLI versions on your local machine and your cluster do not match, either [update your cluster](/docs/containers?topic=containers-update) or [install a different CLI version on your local machine](/docs/containers?topic=containers-cs_cli_install#kubectl).


8. For {{site.data.keyword.block_storage_is_short}}, [verify that you have the latest version of the add-on](/docs/containers?topic=containers-vpc-block#vpc-addon-update).


9. For classic block storage, object storage, and Portworx only: Make sure that you installed the latest Helm chart version for the plug-in.

   **Block and object storage**:

   1. Update your Helm chart repositories.
      ```
      helm repo update
      ```
      {: pre}

   2. List the Helm charts in the repository.
      **For classic block storage**:
        ```
        helm search repo iks-charts | grep block-storage-plugin
        ```
        {: pre}

        Example output:
        ```
        iks-charts-stage/ibmcloud-block-storage-plugin	1.5.0        	                                        	A Helm chart for installing ibmcloud block storage plugin   
        iks-charts/ibmcloud-block-storage-plugin      	1.5.0        	                                        	A Helm chart for installing ibmcloud block storage plugin   
        ```
        {: screen}

      **For object storage**:
        ```
        helm search repo ibm-charts | grep object-storage-plugin
        ```
        {: pre}

        Example output:
        ```
        ibm-charts/ibm-object-storage-plugin         	1.0.9        	1.0.9                         	A Helm chart for installing ibmcloud object storage plugin  
        ```
        {: screen}

   3. List the installed Helm charts in your cluster and compare the version that you installed with the version that is available.
      ```
      helm list --all-namespaces
      ```
      {: pre}

   4. If a more recent version is available, install this version. For instructions, see [Updating the {{site.data.keyword.cloud_notm}} Block Storage plug-in](/docs/containers?topic=containers-block_storage#update_block) and [Updating the {{site.data.keyword.cos_full_notm}} plug-in](/docs/containers?topic=containers-object_storage#update_cos_plugin).

   **Portworx**:

   1. Find the [latest Helm chart version](https://github.com/IBM/charts/tree/master/community/portworx){: external} that is available.

   2. List the installed Helm charts in your cluster and compare the version that you installed with the version that is available.
      ```
      helm list --all-namespaces
      ```
      {: pre}

   3. If a more recent version is available, install this version. For instructions, see [Updating Portworx in your cluster](/docs/containers?topic=containers-portworx#update_portworx).


