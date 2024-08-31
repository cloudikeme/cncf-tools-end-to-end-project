# GKE Cluster Setup Using gcloud CLI

## Introduction

Here's a step-by-step guide on how to create a Google Cloud project, enable the Kubernetes API, and create a GKE cluster:

## **Prerequisites**

Before we proceed with configuring Kubernetes and setting up the GKE cluster, ensure that you have completed the following prerequisites:

1. **Google Cloud SDK Installed**: Make sure you have the Google Cloud SDK installed on your local machine. You can install it by following the [Google Cloud SDK installation guide](https://cloud.google.com/sdk/docs/install).

2. **gcloud CLI Configured**: Ensure that the `gcloud` CLI is configured with your Google Cloud account. You can authenticate your account and set a default project by running:

   ```bash
   gcloud init
   ```

3. **Kubernetes CLI (kubectl) Installed**: Ensure that `kubectl` is installed, as it is necessary for interacting with your Kubernetes clusters. If you haven't installed it yet, you can install it using the command ( for ubuntu):

   ```bash
   sudo apt-get install kubectl
   ```

4. **Basic Knowledge of Git**: You should have a basic understanding of Git, including how to use `.gitignore` to prevent sensitive files from being tracked in your version control.

5. **Git Installed**: Ensure that Git is installed on your local machine to manage your project’s version control.

6. **Access to a Google Cloud Project**: You should have access to a Google Cloud project where you have permissions to create and manage resources, including enabling APIs and creating clusters.

7. **`yq` Installed**: Make sure `yq` is installed, as it’s used to modify YAML files. You can install it by following the [yq installation guide](https://github.com/mikefarah/yq).

8. **Text Editor**: Have a text editor ready for editing configuration files if needed. Examples include VSCode, Sublime Text, or any command-line editor like Vim or Nano.

With these prerequisites in place, you're ready to proceed with configuring Kubernetes and setting up your GKE cluster.

## **Step 1: Set Up the GKE gcloud Auth Plugin**

First, we need to enable the Google Kubernetes Engine (GKE) gcloud auth plugin by exporting the following environment variable:

```bash
export USE_GKE_GCLOUD_AUTH_PLUGIN=True
```

## **Step 2: Create a Google Cloud Project**

First, cd into the `cluster-setup` directory

Next, let's create a new project. We will generate a unique project ID using my name , the current date, and time to ensure it’s globally unique:

```bash
export PROJECT_ID=ikeme-$(date +%Y%m%d%H%M%S)
```

To make this hands on, replace my name "ikeme" , with yours ( unless you really do love my name *wink*)

Next, we update the `settings.yaml` file with the newly created project ID:

```bash
yq --inplace ".google.projectId = \"$PROJECT_ID\"" settings.yaml
```

Now, we proceed to create the project in Google Cloud:

```bash
gcloud projects create $PROJECT_ID
```

After the project is created, run the following command to get a URL to enable the Kubernetes API. 

```bash
echo "https://console.cloud.google.com/marketplace/product/google/container.googleapis.com?project=$PROJECT_ID"
```

Copy the URL and open it in your web browser. If you are prompted to enable billing follow the prompts.

## **Step 3: Enable the Kubernetes API**

In your browser, after opening the provided URL, enable the Kubernetes API for your project.
Note: You may see a prompt asking you to enable billing before you proceed , follow the prompt.


This step is crucial as it allows us to use Kubernetes Engine services in the newly created project.

## **Step 4: Create a GKE Cluster**

Once the Kubernetes API is enabled, we can create a GKE cluster. The command below creates a cluster named `dot` in the `us-east1` region with one node of type `e2-standard-4`. We also enable network policy and disable automatic upgrades:

```bash
gcloud container clusters create  --project $PROJECT_ID \
    --zone us-east1 --machine-type e2-small \
    --num-nodes 1 --enable-network-policy \
    --disk-type "pd-balanced" \
    --disk-size "20" \
    --no-enable-autoupgrade
```
## **Step 5: Generate the kubeconfig File**

Finally, we generate the kubeconfig file, which allows us to interact with the Kubernetes cluster using `kubectl`. The following command configures the credentials for the cluster we just created:

```bash
gcloud container clusters get-credentials dot \
    --project $PROJECT_ID --region us-east1
```

By completing these steps, we've successfully set up a Google Cloud project, enabled the Kubernetes API, created a GKE cluster, and configured our local environment to manage the cluster.

To ensure that Kubernetes stores its configuration in a specific file and to prepare for future use, follow these steps:

## **Step 6: Set the KUBECONFIG Environment Variable**

First, we need to tell our cluster creation tool to use the `kubeconfig-dev.yaml` file for storing Kubernetes configuration. We do this by setting the `KUBECONFIG` environment variable:

```bash
export KUBECONFIG=$PWD/kubeconfig-dev.yaml
```

This command ensures that Kubernetes stores its configuration in the `kubeconfig-dev.yaml` file located in the current working directory. Since this file is already added to `.gitignore`, it won't be tracked by Git, keeping your configuration secure.

## **Step 7: Create a Hidden .env File**

Next, we'll create a hidden `.env` file where we can store important environment variables that we might need later. This file will hold a command to set a `KUBECONFIG_DEV` variable pointing to the path of our `kubeconfig-dev.yaml` file.

To add this command to the `.env` file, run the following command:

```bash
echo "export KUBECONFIG_DEV=$PWD/kubeconfig-dev.yaml" >> .env
```

This command appends the line `export KUBECONFIG_DEV=$PWD/kubeconfig-dev.yaml` to the `.env` file. This setup is useful because, depending on your project’s requirements, you might need to reference this configuration file later on.

By completing these steps, we have successfully directed Kubernetes to use a specific configuration file and stored the path in a hidden environment file for future reference.