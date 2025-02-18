---

copyright:
  years: 2014, 2021
lastupdated: "2021-05-14"

keywords: kubernetes, iks

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
  
  

# Setting up continuous integration and delivery
{: #cicd}

With {{site.data.keyword.cloud_notm}} and other open source tools, you can set up continuous integration and delivery (CI/CD), version control, tool chains, and more to help automate app development and deployment.
{: shortdesc}

Continuous integration (CI) can help you detect errors early, ensure early system integration, and improve collaboration in your development process. Continuous delivery (CD) is a practice by which you build and deploy your software so that it can be released into production at any time. By automating your continuous integration and delivery processes through setting up CI/CD pipelines, you can achieve repeatability in code releases and greater predictability in delivery schedules.

For a deep dive into the benefits and specifics of automating continuous integration and delivery, check out [Automate continuous integration](https://www.ibm.com/garage/method/practices/code/practice_continuous_integration){: external} and [Build and deploy by using continuous delivery](https://www.ibm.com/garage/method/practices/deliver/practice_continuous_delivery){: external} in the IBM Garage Methodology documentation.

## Supported automation tools
{: #cicd_strategy}

To automate your CI/CD pipeline, check out the following supported integrations and tools.
{: shortdesc}

<dl>
<dt>Codeship</dt>
<dd>You can use [Codeship ![External link icon](../icons/launch-glyph.svg "External link icon")](https://codeship.com) for the continuous integration and delivery of containers. For more information, see [Using Codeship Pro To Deploy Workloads to {{site.data.keyword.containerlong_notm}} ![External link icon](../icons/launch-glyph.svg "External link icon")](https://www.ibm.com/cloud/blog/using-codeship-pro-deploy-workloads-ibm-container-service).</dd>
<dt>Grafeas</dt>
<dd>[Grafeas ![External link icon](../icons/launch-glyph.svg "External link icon")](https://grafeas.io) is an open source CI/CD service that provides a common way for how to retrieve, store, and exchange metadata during the software supply chain process. For example, if you integrate Grafeas into your app build process, Grafeas can store information about the initiator of the build request, vulnerability scan results, and quality assurance sign-off so that you can make an informed decision if an app can be deployed to production. You can use this metadata in audits or to prove compliance for your software supply chain.</dd>
<dt>{{site.data.keyword.deliverypipelinelong}}</dt>
<dd>With your app configuration files organized in a source control management system such as Git, you can build your pipeline to test and deploy code to different environments, such as `test` and `prod`. {{site.data.keyword.contdelivery_full}} allows you to automate your app builds and container deployments to Kubernetes clusters by using a toolchain. To get started, see [Setting up a continuous delivery pipeline for a cluster](#continuous-delivery-pipeline). You can also check out [this tutorial on Continuous Deployment to Kubernetes](/docs/solution-tutorials?topic=solution-tutorials-continuous-deployment-to-kubernetes#continuous-deployment-to-kubernetes).</dd>
<dt>Helm</dt>
<dd>[Helm ![External link icon](../icons/launch-glyph.svg "External link icon")](https://helm.sh) is a Kubernetes package manager. You can create new Helm charts or use preexisting Helm charts to define, install, and upgrade complex Kubernetes applications that run in {{site.data.keyword.containerlong_notm}} clusters. For example, you can specify all Kubernetes resources that your app requires in a Helm chart. Then, you can use Helm to create the YAML configuration files and deploy these files in your cluster. You can also [integrate {{site.data.keyword.cloud_notm}}-provided Helm charts ![External link icon](../icons/launch-glyph.svg "External link icon")](https://cloud.ibm.com/kubernetes/helm) to extend your cluster's capabilities, such as with a block storage plug-in. For more information, see [Setting up Helm in {{site.data.keyword.containerlong_notm}}](/docs/containers?topic=containers-helm).</dd>
<dt>Kustomize</dt>
<dd>Use the Kubernetes project [Kustomize ![External link icon](../icons/launch-glyph.svg "External link icon")](https://github.com/kubernetes-sigs/kustomize) to package your apps by both standardizing and customizing your deployments across multiple environments. Kustomize helps you write, customize, and reuse your Kubernetes resource YAML configurations. To get started, see [Packaging apps for reuse in multiple environments with Kustomize](/docs/containers?topic=containers-app#kustomize).</dd>
<dt>Razee</dt>
<dd>[Razee ![External link icon](../icons/launch-glyph.svg "External link icon")](https://razee.io/) is an open-source project that automates and manages the deployment of Kubernetes resources across clusters, environments, and cloud providers, and helps you to visualize deployment information for your resources so that you can monitor the rollout process and find deployment issues more quickly. For more information about Razee and how to set up Razee in your cluster to automate your deployment process, see the [Razee documentation ![External link icon](../icons/launch-glyph.svg "External link icon")](https://github.com/razee-io/Razee).</dd>
</dl>

<br />

## Setting up a continuous delivery pipeline for a cluster
{: #continuous-delivery-pipeline}

Adopt a DevOps approach by using {{site.data.keyword.deliverypipelinelong}}, which includes open toolchains that automate the building and deployment of containerized apps.
{: shortdesc}

Want a more detailed walkthrough? Check out the [Developing a Kubernetes app toolchain with Code Risk Analyzer](https://www.ibm.com/cloud/architecture/tutorials/develop-kubernetes-app-with-code-risk-analyzer){: external} tutorial.
{: tip}

Before you begin, make sure that you have at least the following permissions in {{site.data.keyword.cloud_notm}} IAM:
*  **Editor** platform access role and **Writer** service access role to the **Kubernetes Service** cluster. For more information, see [User access permissions](/docs/containers?topic=containers-access_reference).
*  **Viewer** platform access role to the resource group of the cluster where you want to create the toolchain.
*  **Editor** platform access role to the **Toolchain** service (note that this service is separate than **Continuous Delivery**).

To add a continuous delivery pipeline to your cluster:

1. From the [cluster dashboard](https://cloud.ibm.com/kubernetes/clusters), select the cluster for which you want to set up a continuous delivery pipeline.
2. Select the **DevOps** tab.
3. Click **Create a toolchain**.
4. Review the available toolchains. IBM provides pre-defined toolchains that you can use to deploy, test, and monitor Kubernetes-native apps or Helm charts. You can expand each toolchain to find an overview of the tools that are set up for you and to find the scripts in GitHub that are used to configure the toolchain in your cluster. For more information about each toolchain, see [Toolchain templates](/docs/ContinuousDelivery?topic=ContinuousDelivery-cd_about#templates). If you know what tools you want to use, you can create your own toolchain.
5. Select the toolchain that you want to use and click **Create**.
6. Follow the directions in the console to configure your toolchain. Make sure to include the name of your cluster in your toolchain name so that you can easily find the toolchain that is associated with your cluster later. For more information, see [Creating toolchains](/docs/ContinuousDelivery?topic=ContinuousDelivery-toolchains_getting_started).
7. Select **Delivery Pipeline** to review the stages of your continuous integration and continuous delivery pipeline. After you create your toolchain, your pipeline is automatically kicked off and runs through the stages that you configured. Make sure that your stages run successfully and correct any errors.
8. Modify your toolchain. You can add more tools to your toolchain or change the stages of your delivery pipeline.
   1. From the [cluster dashboard](https://cloud.ibm.com/kubernetes/clusters), select the cluster for which you want to set up a continuous delivery pipeline.
   2. Select the **DevOps** tab.
   3. Select the toolchain that you want to modify.

   Having trouble finding the toolchain that you configured for your cluster? If one of the stages in your continuous delivery pipeline fails, the toolchain does not show in the **DevOps** tab of your cluster. Make sure to review errors in your pipeline by selecting your toolchain from the [{{site.data.keyword.contdelivery_short}} dashboard](https://cloud.ibm.com/devops/toolchains) directly.
   {: tip}


