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
 


# Why does the cluster autoscaler add-on fail with the pod stuck in `Init` state?
{: #ca_ts_secret}

{: tsSymptoms}
When you deploy the cluster autoscaler add-on, the `ibm-iks-cluster-autoscaler` pods are stuck in the `Init` state. When you run the `kubectl get pods | grep auto` command, you see output similar to the following:

```
ibm-iks-cluster-autoscaler1-5656d89447-rrbgm     0/1     Init:0/1   0          5m2s
```
{: screen}

{: tsCauses}
The cluster autoscaler add-on could not validate the secret that you configured. The Kubernetes secret that you used to deploy the cluster autoscaler add-on does not have the required permissions.

{: tsResolve}
To verify that the issue is a secret validation issue, get the logs from the `secret-validator` container in the `ibm-iks-cluster-autoscaler` pod.

1. Get the name of the autoscaler pod in your cluster.
    ```sh
    kubectl get pods -A | grep auto
    ```
    {: pre}

    **Example output**
    ```sh
    kube-system                                             ibm-iks-cluster-autoscaler-6d7bc9b9df-9hgg2                       1/1     Running       0          34m
    ```
    {: screen}

2. Get the logs from the `secret-validator` container. If your secrets are invalid, the output message includes `Invalid secrets`.
    ```sh
    kubectl logs <autoscaler_pod_name> -c secret-validator -n kube-system
    ```
    {: pre}

    **Example output**
    ```sh
    ...
    "msg":"Invalid secrets"}
    ...
    {: screen}

3. After you verified that the issue is related to invalid secrets, verify that you have the `storage-secret-store` secret in your cluster.
    ```sh
    kubectl get secrets -A | grep storage-secret-store
    ```
    {: pre}

4. Update your `storage-secret-store` secret with the requried IAM permissions by [resetting your credentials](/docs/openshift?topic=openshift-cs_troubleshoot_storage#missing_permissions).
