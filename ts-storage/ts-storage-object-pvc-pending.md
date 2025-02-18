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
 


# Object storage: Why does my PVC remain in a pending state?
{: #cos_pvc_pending}

**Infrastructure provider**:
* <img src="../images/icon-classic.png" alt="Classic infrastructure provider icon" width="15" style="width:15px; border-style: none"/> Classic
* <img src="../images/icon-vpc.png" alt="VPC infrastructure provider icon" width="15" style="width:15px; border-style: none"/> VPC



{: tsSymptoms}
When you create a PVC and you run `kubectl get pvc <pvc_name>`, your PVC remains in a **Pending** state, even after waiting for some time.

{: tsCauses}
During the PVC creation and binding, many different tasks are executed by the {{site.data.keyword.cos_full_notm}} plug-in. Each task can fail and cause a different error message.

{: tsResolve}

1. Find the root cause for why the PVC remains in a **Pending** state.
   ```
   kubectl describe pvc <pvc_name> -n <namespace>
   ```
   {: pre}

2. Review common error message descriptions and resolutions.

   <table summary="The columns are read from left to right. The first column has the symptom or error message. The second column describes the message. The third column provides steps to resolve the error.">
   <thead>
   <th>Error message</th>
   <th>Description</th>
   <th>Steps to resolve</th>
   </thead>
   <tbody>
   <tr>
   <td>`cannot get credentials: cannot get secret <secret_name>: secrets "<secret_name>" not found`</td>
   <td>The Kubernetes secret that holds your {{site.data.keyword.cos_full_notm}} service credentials does not exist in the same namespace as the PVC or pod. </td>
   <td>See [PVC or pod creation fails due to not finding the Kubernetes secret](#cos_secret_access_fails).</td>
   </tr>
   <tr>
   <td>`cannot get credentials: Wrong Secret Type.Provided secret of type XXXX.Expected type ibm/ibmc-s3fs`</td>
   <td>The Kubernetes secret that you created for your {{site.data.keyword.cos_full_notm}} service instance does not include the `type: ibm/ibmc-s3fs`.</td>
   <td>Edit the Kubernetes secret that holds your {{site.data.keyword.cos_full_notm}} credentials to add or change the `type` to `ibm/ibmc-s3fs`. </td>
   </tr>
   <tr>
   <td>`Bad value for ibm.io/object-store-endpoint XXXX: scheme is missing. Must be of the form http://<hostname> or https://<hostname>`</br> </br> <code>Bad value for ibm.io/iam-endpoint XXXX: scheme is missing. Must be of the form http://<hostname> or https://<hostname></code></td>
   <td>The s3fs API or IAM API endpoint has the wrong format, or the s3fs API endpoint could not be retrieved based on your cluster location.  </td>
   <td>See [PVC creation fails due to wrong s3fs API endpoint](#cos_api_endpoint_failure).</td>
   </tr>
   <tr>
   <td>`object-path cannot be set when auto-create is enabled`</td>
   <td>You specified an existing subdirectory in your bucket that you want to mount to your PVC by using the `ibm.io/object-path` annotation. If you set a subdirectory, you must disable the bucket auto-create feature.  </td>
   <td>In your PVC, set `ibm.io/auto-create-bucket: "false"` and provide the name of the existing bucket in `ibm.io/bucket`.</td>
   </tr>
   <tr>
   <td>`bucket auto-create must be enabled when bucket auto-delete is enabled`</td>
   <td>In your PVC, you set `ibm.io/auto-delete-bucket: true` to automatically delete your data, the bucket, and the PV when you remove the PVC. This option requires `ibm.io/auto-create-bucket` to be set to <strong>true</strong>, and `ibm.io/bucket` to be set to `""` at the same time.</td>
   <td>In your PVC, set `ibm.io/auto-create-bucket: true` and `ibm.io/bucket: ""` so that your bucket is automatically created with a name with the format `tmp-s3fs-xxxx`. </td>
   </tr>
   <tr>
   <td>`bucket cannot be set when auto-delete is enabled`</td>
   <td>In your PVC, you set `ibm.io/auto-delete-bucket: true` to automatically delete your data, the bucket, and the PV when you remove the PVC. This option requires `ibm.io/auto-create-bucket` to be set to <strong>true</strong>, and `ibm.io/bucket` to be set to `""` at the same time.</td>
   <td>In your PVC, set `ibm.io/auto-create-bucket: true` and `ibm.io/bucket: ""` so that your bucket is automatically created with a name with the format `tmp-s3fs-xxxx`. </td>
   </tr>
   <tr>
   <td>`cannot create bucket using API key without service-instance-id`</td>
   <td>If you want to use IAM API keys to access your {{site.data.keyword.cos_full_notm}} service instance, you must store the API key and the ID of the {{site.data.keyword.cos_full_notm}} service instance in a Kubernetes secret.  </td>
   <td>See [Creating a secret for the object storage service credentials](/docs/containers?topic=containers-object_storage#create_cos_secret). </td>
   </tr>
   <tr>
   <td>`object-path “<subdirectory_name>” not found inside bucket <bucket_name>`</td>
   <td>You specified an existing subdirectory in your bucket that you want to mount to your PVC by using the `ibm.io/object-path` annotation. This subdirectory could not be found in the bucket that you specified. </td>
   <td>Verify that the subdirectory that you specified in `ibm.io/object-path` exists in the bucket that you specified in `ibm.io/bucket`. </td>
   </tr>
   <tr>
   <td>`BucketAlreadyExists: The requested bucket name is not available. The bucket namespace is shared by all users of the system. Please select a different name and try again.`</td>
   <td>You set `ibm.io/auto-create-bucket: true` and specified a bucket name at the same time, or you specified a bucket name that already exists in {{site.data.keyword.cos_full_notm}}. Bucket names must be unique across all service instances and regions in {{site.data.keyword.cos_full_notm}}. </td>
   <td>Make sure that you set `ibm.io/auto-create-bucket: false` and that you provide a bucket name that is unique in {{site.data.keyword.cos_full_notm}}. If you want to use the {{site.data.keyword.cos_full_notm}} plug-in to automatically create a bucket name for you, set `ibm.io/auto-create-bucket: true` and `ibm.io/bucket: ""`. Your bucket is created with a unique name in the format `tmp-s3fs-xxxx`. If you want to specify a bucket name for the auto-created bucket, set `ibm.io/auto-create-bucket: true` and `ibm.io/auto-delete-bucket: false` and `ibm.io/bucket: "<bucket_name>"`.</td>
   </tr>
   <tr>
   <td>`cannot access bucket <bucket_name>: NotFound: Not Found`</td>
   <td>You tried to access a bucket that you did not create, or the storage class and s3fs API endpoint that you specified do not match the storage class and s3fs API endpoint that were used when the bucket was created. </td>
   <td>See [Cannot access an existing bucket](#cos_access_bucket_fails). </td>
   </tr>
   <tr>
   <td>`Put https://s3-api.dal-us-geo.objectstorage.service.networklayer.com/<bucket_name>: net/http: invalid header field value "AWS4-HMAC-SHA256 Credential=1234a12a123a123a1a123aa1a123a123\n\n/20190412/us-standard/s3/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=12aa1abc123456aabb12aas12aa123456sb123456abc" for key Authorization`</td>
   <td>The values in your Kubernetes secret are not correctly encoded to base64.  </td>
   <td>Review the values in your Kubernetes secret and encode every value to base64. You can also use the [`kubectl create secret`](/docs/containers?topic=containers-object_storage#create_cos_secret) command to create a new secret and let Kubernetes automatically encode your values to base64. </td>
   </tr>
   <tr>
   <td>`cannot access bucket <bucket_name>: Forbidden: Forbidden`</td>
   <td>You specified `ibm.io/auto-create-bucket: false` and tried to access a bucket that you did not create or the secret access key or access key ID of your {{site.data.keyword.cos_full_notm}} HMAC credentials are incorrect.  </td>
   <td>You cannot access a bucket that you did not create. Automatically create a bucket instead by setting `ibm.io/auto-create-bucket: true` and `ibm.io/bucket: ""`. If you are the owner of the bucket, see [PVC creation fails due to wrong credentials or access denied](#cred_failure) to check your credentials. </td>
   </tr>
   <tr>
   <td>`cannot create bucket <bucket_name>: AccessDenied: Access Denied`</td>
   <td>You specified `ibm.io/auto-create-bucket: true` to automatically create a bucket in {{site.data.keyword.cos_full_notm}}, but the credentials that you provided in the Kubernetes secret are assigned the **Reader** IAM service access role. This role does not allow bucket creation in {{site.data.keyword.cos_full_notm}}. </td>
   <td>See [PVC creation fails due to wrong credentials or access denied](#cred_failure). </td>
   </tr>
   <tr>
   <td>`cannot create bucket <bucket_name>: AccessForbidden: Access Forbidden`</td>
   <td>You specified `ibm.io/auto-create-bucket: true` and provided a name of an existing bucket in `ibm.io/bucket`. In addition the credentials that you provided in the Kubernetes secret are assigned the **Reader** IAM service access role. This role does not allow bucket creation in {{site.data.keyword.cos_full_notm}}. </td>
   <td>To use an existing bucket, set `ibm.io/auto-create-bucket: false` and provide the name of your existing bucket in `ibm.io/bucket`. To automatically create a bucket by using your existing Kubernetes secret, set `ibm.io/bucket: ""` and follow [PVC creation fails due to wrong credentials or access denied](#cred_failure) to verify the credentials in your Kubernetes secret.</td>
   </tr>
   <tr>
   <td>`cannot create bucket <bucket_name>: SignatureDoesNotMatch: The request signature we calculated does not match the signature you provided. Check your AWS Secret Access Key and signing method. For more information, see REST Authentication and SOAP Authentication for details`</td>
   <td>The {{site.data.keyword.cos_full_notm}} secret access key of your HMAC credentials that you provided in your Kubernetes secret is not correct. </td>
   <td>See [PVC creation fails due to wrong credentials or access denied](#cred_failure).</td>
   </tr>
   <tr>
   <td>`cannot create bucket <bucket_name>: InvalidAccessKeyId: The AWS Access Key ID you provided does not exist in our records`</td>
   <td>The {{site.data.keyword.cos_full_notm}} access key ID or the secret access key of your HMAC credentials that you provided in your Kubernetes secret is not correct.</td>
   <td>See [PVC creation fails due to wrong credentials or access denied](#cred_failure). </td>
   </tr>
   <tr>
   <td>`cannot create bucket <bucket_name>: CredentialsEndpointError: failed to load credentials` </br> </br> `cannot access bucket <bucket_name>: CredentialsEndpointError: failed to load credentials`</td>
   <td>The {{site.data.keyword.cos_full_notm}} API key of your IAM credentials and the GUID of your {{site.data.keyword.cos_full_notm}} service instance are not correct.</td>
   <td>See [PVC creation fails due to wrong credentials or access denied](#cred_failure). </td>
   </tr>
   <tr>
   <td><code>TokenManagerRetrieveError: error retrieving the token</code></td>
   <td>This error occurs when you create a PVC with IAM credentials on a cluster that does not have public outbound access.</td>
   <td>If your cluster does not have public outbound access, [create a {{site.data.keyword.cos_full_notm}} instance that uses HMAC credentials](/docs/containers?topic=containers-object_storage#create_cos_service).</td>
   </tr>
   <tr>
   <td><code>set-access-policy not supported for classic cluster</code></td>
   <td>This error occurs when you install the <code>ibm-object-storage-plugin</code> in a Classic cluster and set the <code>bucketAccessPolicy=true</code> flag. The <code>bucketAccessPolicy=true</code> flag is only used with VPC clusters.</td>
   <td>[Install the plug-in](/docs/containers?topic=containers-object_storage#install_cos) and set the <code>bucketAccessPolicy=false</code> flag.</td>
   </tr>
   </tbody>
   </table>

