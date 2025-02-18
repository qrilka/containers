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
  
  

# Why does ALB secret creation or deletion fail?
{: #cs_albsecret_fails}

**Infrastructure provider**:
* <img src="../images/icon-classic.png" alt="Classic infrastructure provider icon" width="15" style="width:15px; border-style: none"/> Classic
* <img src="../images/icon-vpc.png" alt="VPC infrastructure provider icon" width="15" style="width:15px; border-style: none"/> VPC

{: tsSymptoms}
* After you deploy an Ingress application load balancer (ALB) secret to your cluster by using the `ibmcloud ks ingress secret create` command, the `Description` field is not updating with the secret name when you view your certificate in {{site.data.keyword.cloudcerts_full_notm}}.
* When you list information about the ALB secret, the state says `*_failed`. For example, `create_failed`, `update_failed`, `delete_failed`.

{: tsResolve}
Review the following reasons why the ALB secret might fail and the corresponding troubleshooting steps.

Before you begin, list the ALB secret details (`ibmcloud ks ingress secretget`) and view the ALB secret `status` to get more information on the reason for failure.
{: tip}

|Why it's happening|How to fix it|
|--- |--- |
|The owner of the cluster's API Key does not have the required access roles to download and update certificate data.|Check with your account Administrator to assign the owner of the cluster's API Key, the following {{site.data.keyword.cloud_notm}} IAM roles:<ul><li>The **Manager** and **Writer** service access roles for your {{site.data.keyword.cloudcerts_full_notm}} instance. For more information, see Managing service access for {{site.data.keyword.cloudcerts_short}}.</li><li>The **Administrator** platform access role for the cluster.</li></ul>|
|The certificate CRN provided at time of create, update, or remove does not belong to the same account as the cluster.|Check that the certificate CRN you provided is imported to an instance of the {{site.data.keyword.cloudcerts_short}} service that is deployed in the same account as your cluster.|
|The certificate CRN provided at time of create is incorrect.|<ol><li>Check the accuracy of the certificate CRN string you provide.</li><li>If the certificate CRN is found to be accurate, then try to update the secret: ibmcloud ks ingress secret update --cluster <cluster_name_or_ID> --name <secret_name> --namespace <namespace> --cert-crn <certificate_CRN></li><li>If this command results in the update_failed status, then remove the secret: ibmcloud ks ingress secret rm --cluster <cluster_name_or_ID> --name <secret_name> --namespace <namespace></li><li>Deploy the secret again: ibmcloud ks ingress secret create --cluster <cluster_name_or_ID> --name <secret_name> --cert-crn <certificate_CRN> --namespace <namespace></li></ol>|
|The certificate CRN provided at time of update is incorrect.|<ol><li>Check the accuracy of the certificate CRN string you provide.</li><li>If the certificate CRN is found to be accurate, then remove the secret: ibmcloud ks ingress secret rm --cluster <cluster_name_or_ID> --name <secret_name> --namespace <namespace></li><li>Deploy the secret again: ibmcloud ks ingress secret create --cluster <cluster_name_or_ID> --name <secret_name> --cert-crn <certificate_CRN> --namespace <namespace></li><li>Try to update the secret: ibmcloud ks ingress secret update --cluster <cluster_name_or_ID> --name <secret_name> --namespace <namespace> --cert-crn <certificate_CRN></li></ol>|
|The {{site.data.keyword.cloudcerts_long_notm}} service is experiencing downtime.|Check that your {{site.data.keyword.cloudcerts_short}} service is up and running.|
|Your imported secret has the same name as the IBM-provided Ingress secret.|Rename your secret. You can check the name of the IBM-provided Ingress secret by running `ibmcloud ks cluster get --cluster  | grep Ingress`.|
|The description for the certificate is not updated with the secret name when you view the certificate in {{site.data.keyword.cloudcerts_full_notm}}.|Check whether the length of the certificate description reached the upper limit of 1024 characters.|
{: caption="Troubleshooting Ingress application load balancer secrets"}
{: summary="The columns are read from left to right. The first column describes why the issue happens. The second column provides steps to resolve the error."}
