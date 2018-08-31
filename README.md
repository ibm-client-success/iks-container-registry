<img width="71" alt="image" src="https://media.github.ibm.com/user/20538/files/099741b0-6f73-11e8-9396-35e9e2697962"> <img width="35" alt="image" src="https://media.github.ibm.com/user/20538/files/879806f0-6f71-11e8-9d14-59cc8d78bc41">

## IBM Cloud Container Registry - Scanning & Monitoring the Vulnerability of Images

---
This repository provides an overview and high-level instructions about Image scanning results inside a **namespace** using **IBM Cloud Container Registry Vulnerability Advisor** inside a [Bluemix](https://console.bluemix.net) environment. 

These findings and instructions provided in this repository assume that the user has a [Bluemix account](https://console.bluemix.net), has completed the [setup instructions](https://console.bluemix.net/docs/services/Registry/index.html#index) and **all** required CLIs as well as other prerequisites, already configured a [namespace](https://console.bluemix.net/docs/services/Registry/index.html#registry_namespace_add) and is running [cluster(s)](https://console.bluemix.net/docs/containers/cs_tutorials.html#cs_cluster_tutorial) inside the Bluemix account.

# Image Security with Vulnerability Advisor
---

## Overview

Supported operating system images in a Bluemix repository are scanned by the **IBM Cloud Container Scanner** which uses a microservice that scans the containers in a Kubernetes cluster for risks, missing patches and other potential vulnerabilities. "_This microservice runs in the [kube-system](https://console.bluemix.net/docs/containers/cs_integrations.html#integrations) namespace_". The output of the results are provided by the [Vulnerability Advisor](https://console.bluemix.net/docs/services/va/va_index.html) reporting detailed information about the vulnerabilities of an image. 

Once an image scan is complete the Vulnerability Advisor provides a detailed report whether the image has passed a scan or whether the image is vulnerable and requires updates/patches. "_The possible vulnerabilities are updated daily from published security notices for the Docker image types_". The updated image can be downloaded to the local machine and pushed back to the repository using [docker commands](https://docs.docker.com/engine/reference/commandline/push/) to meet security requirements detected by the Vulnerability Advisor. Though, in some unique cases some images require manual update on a localhost for the reported security issues before they can be pushed back to the repository.

---
## Discoveries

A playground environment was created in Bluemix using the instruction provided in [IBM Cloud Docs](https://console.bluemix.net/docs/services/Registry/registry_setup_cli_namespace.html#registry_setup_cli_namespace) to configure and to test the functionalities of the IBM Cloud Container Scanner and to view the report provided by the Vulnerability Advisor. In this particular environment, accessed using the CLI method, a cluster **figure 1** and a namespace **figure 2** were configured and implemented. 

Using the [docker commands](https://docs.docker.com/engine/reference/commandline/push/) from the terminal, docker images were pushed to the namespace in the Bluemix environment **figure 3**. A sample report result provided by the Vulnerability Advisor below, **figure 3**, displays **httpd** image has security issues once it was scanned by the microservice. In this example, only one image, *httpd*, has a security issue while the other images have passed the security scan. 

#### Figure 1 - Cluster
<img width="1355" alt="image" src="https://media.github.ibm.com/user/20538/files/73879706-6f1d-11e8-89d5-471c0d2023e5">

#### Figure 2 - namespace Repository
<img width="457" alt="image" src="https://media.github.ibm.com/user/20538/files/8443d89a-6f20-11e8-98d3-4315a5582125">

#### Figure 3 - Docker Images Inside a namespace Repository
<img width="1003" alt="image" src="https://media.github.ibm.com/user/20538/files/a16c4654-6f21-11e8-8586-c247cc02cbfd">

The security reports are accessible via the GUI from the [Bluemix console](https://console.bluemix.net) which provides a detailed, user-friendly dashboard about the vulnerabilities of an image residing in the namespace. Furthermore, by clicking on an image in the GUI, information about specific vulnerabilities and how to resolve the listed security issues becomes visible the specific image. 

#### Figure 4 - Docker Images Residing in a namespace in a Private Repository 
<img width="1425" alt="image" src="https://media.github.ibm.com/user/20538/files/47beeeca-6fbe-11e8-9fbc-ebf381ecf895">

#### Figure 5 - A Docker Image with OK Status
<img width="1435" alt="image" src="https://media.github.ibm.com/user/20538/files/38d12b0a-6fbc-11e8-84a2-006aebedfa67">

For example, **figure 6**, httpd image has 6 vulnerabilities reported by the Vulnerability Advisor. In **figure 7**, the same Docker image, details 1 of 6 vulnerabilities further providing an explanation about the specific vulnerability.

#### Figure 6 - Vulnerabilities of a Docker Image
<img width="1426" alt="image" src="https://media.github.ibm.com/user/20538/files/6864ec80-6f62-11e8-8e02-8e08dfd84319">

#### Figure 7 - Detailed Information About a Specific Vulnerability 
<img width="1430" alt="image" src="https://media.github.ibm.com/user/20538/files/a8e17fa4-6f5c-11e8-93d7-eaf541c470ec">

---
## Updating Security Issues of an Image Residing in the Repository   

As mentioned earlier in this document, "_the possible vulnerabilities are updated daily from published security notices for the Docker image types_". The updated image can be downloaded to the local machine and pushed back to the repository using [docker commands](https://docs.docker.com/engine/reference/commandline/push/) to meet security requirements detected by the Vulnerability Advisor. Though, in some unique cases, some images require manual update on a localhost for the reported security issues before they can be pushed back to the repository. 

For example, in **figure 8** below, some Docker commands are provided to **push** or **pull** the particular image. Unless pushing the same image to the repository is intended for a different reason, it's important to execute the Docker **push** command once the particular image is updated.

#### Figure 8 - Docker Common CLI Commands 
<img width="1422" alt="image" src="https://media.github.ibm.com/user/20538/files/1204b7e4-6fd0-11e8-99f5-88e6f6142a31">

# Configure & Deploy Enforcement Deployment

----

## Overview

Image containers can be verified for security issues before they are deployed into a cluster in IBM® Cloud Kubernetes Service. The verification is handled by policy or policies that are in place in the environment. The images can be controlled from where they are deployed, and the policies on them can ensure that they are also coming from a trusted source and meet security compliance. For example, if a particular pod does not meet a specific security policy that is in place, then the cluster will not be modified and the pod will be prevented from being deployed.    

"_IBM Container Image Security Enforcement gets the information about image content trust and vulnerabilities from IBM® Cloud Container Registry. You can choose whether to block or allow images from other registries in your policies, but you cannot use vulnerability or trust enforcement for these images_".

## Discoveries

Figure 1 below is a sample policy, Default cluster-wide policy, that can be implemented in a cluster environment. The focus on this particular policy are the components *trust* and *va*: 

```
policy:
        trust:
          enabled: true
        va:
          enabled: true
```
In summary, these two specifications in this policy determine whether an image meet the security requirements inside the specific cluster. The **trust** section looks for a trusted, signed image where the key is pulled from a trusted server. The **va** section determines if the image has passed or failed a security scan performed by the Vulnerability Advisor. When both **trust** and **va** are set to **true** it indicates that they are both active or as listed in the policy **enabled**. When either one of these settings are set to **false** it means that the policy will bypass that particular component and the image will pass regardless of any existing security issue it may have.

In figures 2 - 4 there are live demonstration screenshots that display the results of a pod deployment affected by these policy settings discussed thus far.

There are other policies similar to the **Default Cluster-Wide policy**, for example: **Default kube-system policy**, **Default ibm-system policy**, and others that are deployed by default in a cluster. A custom policy can be created to meet the needs of a specific cluster environment and are further explained with more details here: [Enforcing container image security (beta)](https://console.bluemix.net/docs/services/Registry/registry_security_enforce.html#security_enforce). 

#### Figure 1 - Default Cluster-Wide policy
<img width="757" alt="image" src="https://media.github.ibm.com/user/20538/files/8a0bdd06-70a4-11e8-962b-8dbcce90f840">

---
#### Figure 2 - Default Cluster-Wide policy set to TRUE in an environment
<img width="674" alt="image" src="https://media.github.ibm.com/user/20538/files/78ce4cc4-70bb-11e8-970a-035a51f0125f">

#### Figure 3 - Deploying a pod using nginx failed due to policy settings listed in figure 2
<img width="1260" alt="image" src="https://media.github.ibm.com/user/20538/files/ed55f9c0-70bb-11e8-9d2b-db54b473c1b7">

#### Figure 4 - Deploying a pod using nginx is successful since policy settings change to FALSE
<img width="711" alt="image" src="https://media.github.ibm.com/user/20538/files/5e226364-70bc-11e8-8f03-752d9ea113d4">

# Livescan - Image Security with Vulnerability Advisor
---
### Overview
Livescan with Vulnerability Advisor provides security management for IBM® Cloud Kubernetes Service. The Vulnerability Advisor generates a security status report, suggests fixes and best practices, and provides management to restrict non-secure images from running inside of a cluster.

### Managing Image Security Live-scan with Vulnerability Advisor
According to Container Service Development member, Kristin Kronstain Brown, this feature Image Security Live-scan is currently **not** available with the Vulnerability Advisor in the production environment at this time. Though, staging documentation can be found [here](https://console.stage1.bluemix.net/docs/services/va/va_index.html#va_reviewing_container).

# Step-by-Step Demonstration - Enforcing Container Image Security (beta) Using Custom Policy

### Prerequisites:

- A cluster with Kubernetes version 1.9 or higher. Tutorial: Creating Kubernetes clusters can be found [here](https://console.bluemix.net/docs/containers/cs_tutorials.html#cs_cluster_tutorial).

- Helm. Setting up Helm and installing default policies can be found [here](https://console.bluemix.net/docs/containers/cs_integrations.html#helm)

- A namespace. How to set up a namespace can be found [here](https://console.bluemix.net/docs/services/Registry/index.html#registry_namespace_add)

```
$ git clone https://github.com/ibm-client-success/iks-container-registry.git
$ cd iks-container-registry
```

### Abstract:

The focus of this demonstration is to secure Docker images before entering a namespace or a cluster using custom IBM Container 
Image Security Enforcement custom policies. This article introduces an overview, considerations, and recommendations using 
custom policies for identifying and preventing vulnerable Docker containers from being deployed in the IBM Cloud environment.

### About IBM Container Image Security Enforcement:

IBM Container Image Security Enforcement can be configured to verify container images before they are deployed to a cluster on 
IBM® Cloud Kubernetes Service. Images are controlled where they are deployed from, enforce Vulnerability Advisor policies, and 
certify that content trust is suitably applied to an image before deployment.

### Container Image Security Enforcement in a Cluster:

IBM Container Image Security Enforcement automatically installs policies to provide a starting point for building security 
policy in a cluster. Additional information and how to install default policies can be found [here](https://console.bluemix.net/docs/services/Registry/registry_security_enforce.html#security_enforce). These policies will apply the default security policies onto all Kubernetes namespaces in the cluster. The default policies that will install are:

**1.	Default Cluster-wide policy** – Enforces that all images in all registries are from a trusted source and Vulnerability 
Advisor has no security reported issues on the image. 

**2.	Default Kube-system policy** – A namespace-wide policy installed specifically for the kube-system namespace.  It allows 
all images for any container registry to be deployed into the kube-system namespace without restriction.  

**3.	Default IBM-system policy** – A namespace-wide policy installed by default. This policy permits all images from any 
container registry to be deployed into the ibm-system without restrictions. Additionally, repositories that are used will be 
permitted to configure the cluster and to install or upgrade Image Security Enforcement.

### What will I explore in this article?

This blog will focus on the default **cluster-wide policy** and its two important flag components: **“va”** and **“trust”** as shown in Figure 1. Before moving forward, it is important to explain that _“va”_ stands for **Vulnerability Advisor** and its primary function is to scan images for any security vulnerabilities. The main function of the _“trust”_ flag is to ensure the image’s integrity, to confirm that it’s **signed**, and be sure that it originated from a trusted source. Signing an image and verifying its integrity is explained in detail [here](https://console.bluemix.net/docs/services/Registry/registry_trusted_content.html#registry_trustedcontent). 

**Figure 1 – An Example of a Default Cluster-Wide Policy.yaml File**

<img width="514" alt="image" src="https://media.github.ibm.com/user/20538/files/3dd6e132-9fd5-11e8-8992-fa2686db4fac">

Figure 2 features a high-level overview of an image that’s been screened by the default cluster-wide policy. Depending on the 
policy’s flag configuration in a cluster, the image will be validated for the next step in its deployment process differently.

**Figure 2 – High-Level Overview of a Custom Policy**

<img width="359" alt="image" src="https://media.github.ibm.com/user/20538/files/623223d4-9fd5-11e8-9f1c-93cd50417eb7">

The figure below shows the four possible combinations of the component flags in a cluster-wide policy and the corresponding result of attempting to deploy an untrustworthy image with vulnerabilities.

**Figure 3 – High-Level Overview of the Custom Policy Possibilities Against an Image**

<img width="381" alt="image" src="https://media.github.ibm.com/user/20538/files/a338563a-9fd7-11e8-8ebc-286419d8ff6b">

### Discovering the outcome of a tailored default cluster-wide policy against an image:

In this section, we’ll see the outcomes of different custom policies applied against an image.

The image list in Figure 4 displays information that can differ when the default cluster-wide policy flags **“trust”** and **“va”** are modified.

```$ bx cr images```

**Figure 4 – IBM Cloud Image Repository**

<img width="516" alt="image" src="https://media.github.ibm.com/user/20538/files/bd1a364c-9fd5-11e8-95ec-38f3d9f11078">

Figure 4 displays the list of images in a namespace. 
As shown, there are few images with security issues and others with no security issues. We can see how the deployment of these images will be affected when we enforce image security using a default Cluster-Wide policy. 

Below, we will see the outcomes of four different cases of custom policies by changing the “trust” and “va” flags from DefaultCluster-wide.yaml , applied against the images.

**Case 1** 

For example, when both the **“trust”** and “va” components in the default cluster-wide policy, exhibited in the `defaultcluster-wide.yaml` file below, are set to **“false”** the policy will **not** prevent a POD deployment regardless of the integrity or any potential vulnerabilities in an image. This means that image deployment and POD creation is allowed without any issues, such as issues reported by Vulnerability Advisor and/or the trustworthiness of an image.

```$ vi defaultcluster-wide.yaml```

```
apiVersion: securityenforcement.admission.cloud.ibm.com/v1beta1
kind: ClusterImagePolicy
metadata:
  name: ibmcloud-default-cluster-image-policy
spec:
   repositories:
# This enforces that all images deployed to this cluster pass trust and VA
# To override, set an ImagePolicy for a specific Kubernetes namespace or  # modify this policy
    - name: "*"
      policy:
        trust:
          enabled: false
        va:
          enabled: false
 ```

```$ kubectl apply -f defaultcluster-wide.yaml```

**Results:**

<img width="518" alt="image" src="https://media.github.ibm.com/user/20538/files/08b06338-9fd6-11e8-9e94-1deb83c11f3e">

Attempting to deploy the **tomcat:latest** image using the `tomcat.yaml` file example:

**Example of tomcat.yaml File**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment
  labels:
    app: tomcat
spec:
  replicas: 2
  selector:
    matchLabels:
      app: tomcat
  template:
    metadata:
      labels:
        app: tomcat
    spec:
      containers:
      - name: tomcat
        image: YOUR_REGISTRY_ADDRESS_LINK/YOUR_SPACE/tomcat:latest
        ports:
        - containerPort: 80
 ```

**Note:**

Make sure you have the correct image path in the yaml file associated with image you want to run or deploy

```$ kubectl apply -f tomcat.yaml```

**Results:**

<img width="342" alt="image" src="https://media.github.ibm.com/user/20538/files/4fdb2da6-9fd6-11e8-9654-14c40c083a01">

```$ kubectl get deployments```

**Results:**

<img width="514" alt="image" src="https://media.github.ibm.com/user/20538/files/69e659b4-9fd6-11e8-8e0e-a8172dfe8022">

```$ kubectl get pods```

**Results:**

<img width="514" alt="image" src="https://media.github.ibm.com/user/20538/files/8456cae0-9fd6-11e8-907f-2ede33bcbb66">

Validate that your image is deployed and is working as expect.

**Clean up**

```$ kubectl delete deployment tomcat-deployment```

**Results:**

<img width="387" alt="image" src="https://media.github.ibm.com/user/20538/files/9dac6c3e-9fd6-11e8-87a5-e914cfad5fb6">

**Case 2** 

Now, let’s leave the default cluster-wide policy **“trust”** to **“false”** and change the **“va”** to **“true”** and see the results. To make this change you have two options:

**Option 1:**
Note: First navigate to the directory where you have cloned the repo, than execute the following commands:

```
sed -i -e '15 s/enabled: false/enabled: true/' contents/defaultcluster-wide.yaml
```

**Option 2:**

```$ vi defaultcluster-wide.yaml```

```
apiVersion: securityenforcement.admission.cloud.ibm.com/v1beta1
kind: ClusterImagePolicy
metadata:
  name: ibmcloud-default-cluster-image-policy
spec:
   repositories:
# This enforces that all images deployed to this cluster pass trust and VA
# To override, set an ImagePolicy for a specific Kubernetes namespace or  # modify this policy
    - name: "*"
      policy:
        trust:
          enabled: false
        va:
          enabled: true
```

```$ kubectl apply -f defaultcluster-wide.yaml```

**Results:**

<img width="514" alt="image" src="https://media.github.ibm.com/user/20538/files/edba0e0c-9fd6-11e8-875d-c2b0870051ad">

```$ kubectl apply -f tomcat.yaml```

**Results:**

<img width="567" alt="image" src="https://media.github.ibm.com/user/20538/files/08e087c4-9fd7-11e8-855b-0d13dc948d24">

As seen in Figure 4, the image tomcat:latest has one issue reported by the Vulnerability Advisor so POD deployment for this particular image is prevented as expected, since “va” flag is set to “true”.

**Case 3:**

To demonstrate the use of “trust” flag, we consider taking an image with no vulnerability issues and try to deploy it. The tomcat:v2 image as show in Figure 4, is a secure image in the IBM Cloud image repository without any vulnerability issues. 
Let’s modify the default cluster-wide policy “trust” to “true” and “va” to “false”, then execute the tomcat-v2.yaml file and analyze the outcome:

**Example of tomcat-v2.yaml File**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcatv2-deployment
  labels:
    app: tomcat
spec:
  replicas: 3
  selector:
    matchLabels:
      app: tomcat
  template:
    metadata:
      labels:
        app: tomcat
    spec:
      containers:
      - name: tomcat
        image: YOUR_REGISTRY_ADDRESS_LINK/YOUR_SPACE/tomcat:v2
        ports:
        - containerPort: 80
```

To modify the `defaultcluster-wide.yaml` you have two options:

**Option 1:**
Note: First navigate to the directory where you have cloned the repo, than execute the following commands:

```
sed -i -e '13 s/enabled: false/enabled: true/' contents/defaultcluster-wide.yaml
sed -i -e '15 s/enabled: true/enabled: false/' contents/defaultcluster-wide.yaml
```

**Option 2:**

```$ vi defaultcluster-wide.yaml```

```
apiVersion: securityenforcement.admission.cloud.ibm.com/v1beta1
kind: ClusterImagePolicy
metadata:
  name: ibmcloud-default-cluster-image-policy
spec:
   repositories:
    # This enforces that all images deployed to this cluster pass trust and VA
    # To override, set an ImagePolicy for a specific Kubernetes namespace or modify this policy
    - name: "*"
      policy:
        trust:
          enabled: true
        va:
          enabled: false
```

```$ kubectl apply -f defaultcluster-wide.yaml```

**Results:**

<img width="515" alt="image" src="https://media.github.ibm.com/user/20538/files/51e4b77e-9fd7-11e8-993d-b54a43560f3f">

```$ kubectl apply -f tomcat-v2.yaml```

**Results:**

<img width="571" alt="image" src="https://media.github.ibm.com/user/20538/files/76e236a0-9fd7-11e8-8ff7-a80acfe1557b">

Deployment of the **tomcat:v2** image has been denied because the system has determined that this particular image was not signed when it was pulled from the source and is untrustworthy.

**Case 4:**

When both flags are set to “true”, as shown in the discoveries so far, the first flag of the default cluster-wide policy is applied first.  Therefore, if the image is not signed and it is not from a trusted source, then the deployment will be denied, and the second flag of the policy will not be applied. Deployment will cease at this point. 

### Rectifying Image Issues to Pass POD Deployment 

Depending on the settings of the default cluster-wide policy components, **“trust”** and **“va”**, there are several ways to remedy image issues in order to successfully deploy in a cluster:

- Pull a more up-to-date image and verify that it is signed from a trusted source. This is the most common method.
- The user can manually update an existing image and sign an image in a repository to ensure the integrity of images in your registry namespace. 
- Create an exception in a repository to bypass an image’s Vulnerability Advisor scan report for a particular reported issue(s).

### Summary

IBM Container Image Security Enforcement custom policies are easy to be configured and most importantly contribute in pushing secure images into a namespace before the image is deployed in a cluster. Ultimately, these custom policies are put in place to safeguard a cloud environment by validating the images before allowing them to be deployed into a Kubernetes cluster.   
