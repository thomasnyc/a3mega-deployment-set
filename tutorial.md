
# **Automating HPC Deployments on Google Cloud using Cluster Toolkit**

This tutorial guides you through setting up a continuous integration (CI) pipeline using **GCP Cluster Toolkit** to automatically deploy high-performance computing (HPC) environments. We'll use the **Google Cloud Cluster Toolkit** (`gcluster`) to define the infrastructure in YAML files.

* **A3mega deployment instruction**
  https://cloud.google.com/cluster-toolkit/docs/deploy/deploy-a3-mega-cluster
* **Google Cloud Cluster Toolkit**
  https://github.com/GoogleCloudPlatform/cluster-toolkit


**Kick off the demo / tutorial**, please click on the link:
https://ssh.cloud.google.com/cloudshell/editor?cloudshell_git_repo=https://github.com/GoogleCloudPlatform/cluster-toolkit

Downloading the files here:
```bash
wget https://github.com/thomasnyc/a3mega-deployment-set/archive/refs/heads/main.zip
unzip main.zip
```

Get the tutorial up:
```bash
teachme a3mega-deployment-set-main/tutorial.md
```

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
```
Note:
Credentials saved to file: [/tmp/xxxxx/application_default_credentials.json]
Copy them to this command below

```bash
export GOOGLE_APPLICATION_CREDENTIALS=
```


* Set the default GCP project:

```bash
gcloud config set project 
```

* You can install terraform, go, packer with this script:
```bash
pushd a3mega-deployment-set-main
chmod 755 cloudshell-install-dependence.sh 
sudo ./cloudshell-install-dependence.sh 
popd
```
* Then we need to download and run **make** command to build the **ghpc and gcluster** command.

```bash
git clone https://github.com/GoogleCloudPlatform/cluster-toolkit
pushd cluster-toolkit
make
./gcluster --version
./gcluster --help
popd
```
---
## **Step 3: Deploy the Cluster 🎉**

Since the deployment and blueprint files are provided by the team, There is no need to update them. 

**Create Configuration Bucket** - this is required to hold the terraform state
```bash
export TF_STATE_BUCKET=oli-arc-bucket
export PROJECT_ID=northam-ce-mlai-tpu
export REGION=us-central1
gcloud storage buckets create gs://${TF_STATE_BUCKET} \
    --project=${PROJECT_ID} \
    --default-storage-class=STANDARD \
    --location=${REGION} \
    --uniform-bucket-level-access
gcloud storage buckets update gs://${TF_STATE_BUCKET} --versioning

```

**Full Deployment** - This is the single click deployment. If you did not yet create the network and built the image, please run this option, after you've updated the values in a3mega-deployment-set-main/a3mega-slurm-deployment-thomashk.yaml (including ensuring that the `TF_STATE_BUCKET`, `PROJECT_ID`, and `REGION` variables are updated to reflect what's above and what you plan on setting up in your environment):

```bash
cluster-toolkit/gcluster deploy -d a3mega-deployment-set-main/a3mega-slurm-deployment-thomashk.yaml a3mega-deployment-set-main/a3mega-lustre-slurm-blueprint.yaml --auto-approve
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
