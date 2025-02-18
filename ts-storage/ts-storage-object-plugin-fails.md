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
 


# Object storage: Why does installing the Object storage plug-in fail?
{: #cos_plugin_fails}

**Infrastructure provider**:
  * <img src="../images/icon-classic.png" alt="Classic infrastructure provider icon" width="15" style="width:15px; border-style: none"/> Classic
  * <img src="../images/icon-vpc.png" alt="VPC infrastructure provider icon" width="15" style="width:15px; border-style: none"/> VPC

{: tsSymptoms}
When you install the `ibm-object-storage-plugin`, the installation fails with an error similar to the following:
```
Error: rendered manifest contains a resource that already exists. Unable to continue with install. Existing resource conflict: namespace: , name: ibmc-s3fs-flex-cross-region, existing_kind: storageClass, new_kind: storage.k8s.io/v1, Kind=StorageClass
Error: plugin "ibmc" exited with error
```
{: screen}

{: tsCauses}
During the installation, many different tasks are executed by the {{site.data.keyword.cos_full_notm}} plug-in such as creating storage classes and cluster role bindings. Some of these resources might already exist in your cluster from previous {{site.data.keyword.cos_full_notm}} plug-in installations and were not properly removed when you removed or upgraded the plug-in.

{: tsResolve}
1. Delete the resource that is displayed in the error message.
   ```
   kubectl delete <resource_kind> <resource_name>
   ```
   {: pre}

   Example for deleting a storage class resource:
   ```
   kubectl delete storageclass <storage_class_name>
   ```
   {: pre}

2. [Retry the installation](/docs/containers?topic=containers-object_storage#install_cos).

3. If you continue to see the same error, get a list of the resources that are installed when the plug-in is installed.

   1. Get a list of storage classes that are created by the `ibmcloud-object-storage-plugin`.
      ```
      kubectl get StorageClass --all-namespaces \
          -l app=ibmcloud-object-storage-plugin \
          -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}'
      ```
      {: pre}

   2. Get a list of cluster role bindings that are created by the `ibmcloud-object-storage-plugin`.
      ```
      kubectl get ClusterRoleBinding --all-namespaces \
          -l app=ibmcloud-object-storage-plugin \
          -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}'
      ```
      {: pre}

   3. Get a list of role bindings that are created by the `ibmcloud-object-storage-driver`.
      ```
      kubectl get RoleBinding --all-namespaces \
         -l app=ibmcloud-object-storage-driver \
         -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\n"}{end}'
      ```
      {: pre}

   4. Get a list of role bindings that are created by the `ibmcloud-object-storage-plugin`.
      ```
      kubectl get RoleBinding --all-namespaces \
          -l app=ibmcloud-object-storage-plugin \
          -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\n"}{end}'
      ```
      {: pre}

   5. Get a list of cluster roles that are created by the `ibmcloud-object-storage-plugin`.
      ```
      kubectl get ClusterRole --all-namespaces \
         -l app=ibmcloud-object-storage-plugin \
         -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}'
      ```
      {: pre}

   6. Get a list of deployments that are created by the `ibmcloud-object-storage-plugin`.
      ```
      kubectl get Deployments --all-namespaces \
         -l app=ibmcloud-object-storage-plugin \
         -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\n"}{end}'
      ```
      {: pre}

   7. Get a list of the daemon sets that are created by the `ibmcloud-object-storage-driver`.
      ```
      kubectl get DaemonSets --all-namespaces \
         -l app=ibmcloud-object-storage-driver \
         -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\n"}{end}'
      ```
      {: pre}

   8. Get a list of the service accounts that are created by the `ibmcloud-object-storage-driver`.
      ```
      kubectl get ServiceAccount --all-namespaces \
         -l app=ibmcloud-object-storage-driver \
         -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\n"}{end}'
      ```
      {: pre }

   9. Get a list of the service accounts that are created by the `ibmcloud-object-storage-plugin`.
      ```
      kubectl get ServiceAccount --all-namespaces \
         -l app=ibmcloud-object-storage-plugin \
         -o jsonpath='{range .items[*]}{.metadata.namespace}{"\t"}{.metadata.name}{"\n"}{end}'
      ```
      {: pre}

4. Delete the conflicting resources.

5. After you delete the conflicting resources, [retry the installation](/docs/containers?topic=containers-object_storage#install_cos).




