
# **Automating HPC Deployments on Google Cloud using Cluster Toolkit**

This tutorial guides you through setting up a continuous integration (CI) pipeline using **GCP Cluster Toolkit** to automatically deploy high-performance computing (HPC) environments. We'll use the **Google Cloud Cluster Toolkit** (`gcluster`) to define the infrastructure in YAML files.

* **A3mega deployment instruction**
  https://cloud.google.com/cluster-toolkit/docs/deploy/deploy-a3-mega-cluster
* **Google Cloud Cluster Toolkit**
  https://github.com/GoogleCloudPlatform/cluster-toolkit


To kick off the demo / tutorial, please click on the link:
https://ssh.cloud.google.com/cloudshell/editor?cloudshell_git_repo=https://github.com/GoogleCloudPlatform/cluster-toolkit



Then copy the 3 files in this repo to the cluster-toolkit directory.
* a3mega-slurm-deployment-thomashk.yaml
* a3mega-lustre-slurm-blueprint.yaml
* tutorial.md

Now, you are ready for running the Prerequisties:

---


## **Prerequisites**

Before you begin, make sure you have the following:


* A **Google Cloud Project** with an active billing account.
* The **<code>gcloud</code> command-line tool** installed and authenticated (`gcloud auth login`) & (`gcloud auth application-default login`) follow the command below: 
* A **Git repository** (e.g., on GitHub, GitLab, or Cloud Source Repositories) containing your Cluster Toolkit configuration files. Cloud shell command shall already clone the repository required.

* Run the gcloud auth login and gcloud auth application-default login 
```bash
gcloud auth login
```

```bash
  gcloud auth application-default login
  export GOOGLE_APPLICATION_CREDENTIALS=
```
Note: 
Credentials saved to file: [/tmp/xxxxx/application_default_credentials.json]
Then copy the full path to export GOOGLE_APPLICATION_CREDENTIALS=xxx.json


* Set the default GCP project:

```bash
  gcloud config set project 
```

* You can install the terraform , go, packer with this script:
```bash
chmod 755 cloudshell-install-dependence.sh 
sudo ./cloudshell-install-dependence.sh 
```
* Then we need to run **make** command to build the **ghpc and gcluster** command.

```bash
make
```
---
## **Step 3: Deploy the Cluster 🎉**

Since the deployment and blueprint files are provided by the team, There is no need to update them. 

**Full Deployment** - This is the single click deployment: 
If you never create the network and the image, please run this option:

```bash
./gcluster deploy -d a3mega-slurm-deployment.yaml a3mega-lustre-slurm-blueprint.yaml --auto-approve
```

Note:  
Image builing process runs between 30 - 40 mins.

Managed Lustre creating process runs between 30 - 40 mins.


**Cluster Deploy / Destory only**  - Deploy / Destroy the cluster only. This is for users who already created the network and have the custom image built. 
If you want to Deploy / Destroy the clsuter only. Please follow the following steps: 

Deploy the cluster only: 

```bash
  gcluster import-inputs a3mega-lustre-base/cluster
  terraform -chdir=a3mega-lustre-base/cluster apply
```

Delele the clsuter only:

```bash
  terraform -chdir=a3mega-lustre-base/cluster destroy
```

---

## **Cleaning Up 🧹**

HPC clusters can be expensive, so it's critical to tear down your resources when you are finished.

You can destroy the deployment by running the `ghpc destroy` command locally with the same deployment file.

```bash
./gcluster destroy a3mega-lustre-base --auto-approve
```
