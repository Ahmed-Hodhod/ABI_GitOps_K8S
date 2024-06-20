# ArgoCD Integrated with HELM & Kustomize

## Video Demo

For a step-by-step walkthrough of the project setup and deployment process, [watch the video demo](https://youtu.be/_v6lA5V4New).

## Wordpress Application Repository 

The docker image is built based off the wordpress application which can be found in this repo, [wordpress-container repository](https://github.com/Ahmed-Hodhod/wordpress-container/tree/master).

## Project Objectives

This project aims to deploy a WordPress application with a MySQL database on a local Kubernetes cluster using various tools and technologies. Below are the key objectives:

- **Create a Local Cluster**: Set up a Kubernetes cluster on your local machine using Minikube.
- **DockerHub Repository**: Create a private repository on DockerHub named “wordpress”.
- **GitHub Repository**: Create a GitHub repository named “wordpress-container”.
- **Push WordPress Code**: Copy the WordPress code from its official repository and push it to your GitHub repository.
- **Dockerfile Configuration**: Create a Dockerfile for WordPress and configure a GitHub Actions workflow to build and push the image to the private DockerHub repository.
- **Install ArgoCD**: Set up ArgoCD on your Kubernetes cluster.
- **Helm Chart Creation**: Develop a Helm chart for the WordPress application.
- **Kustomize Application**: Create a Kustomize application for the project.
- **Automatic Deployment with ArgoCD**: Configure ArgoCD to automatically deploy the application when the image version changes in the Helm values.
- **Private Registry Configuration**: Configure ArgoCD to pull from a private Docker registry.

## Tools and Technologies

The project utilizes the following tools and technologies:

- **Minikube**: To create a local Kubernetes cluster.
- **GitHub Codespace**: For a cloud-based development environment.
- **Kubernetes**: To orchestrate the deployment of the application.
- **Docker**: To containerize the application.
- **Helm Charts**: For Kubernetes application deployment.
- **Kustomize**: To customize Kubernetes configurations.
- **ArgoCD**: For continuous delivery and GitOps-based deployment.
- **Ubuntu**: As the operating system for development and deployment.

## GitHub Actions Workflow

This GitHub Actions workflow automates the process of building a Docker image from the repository, pushing it to Docker Hub, and updating the corresponding values in Helm and Kustomize configuration files. The workflow is triggered by a push event to the `master` branch.

### Workflow Overview

**1. Build and Push Docker Image**

- **Checkout repository**: Uses the `actions/checkout` action to checkout the repository code.
- **Set up Docker Buildx**: Sets up Docker Buildx for building multi-platform images.
- **Log in to Docker Hub**: Authenticates to Docker Hub using secrets for the username and access token.
- **Get short commit hash**: Retrieves the short commit hash of the current commit.
- **Build and push Docker image**: Builds and pushes the Docker image to Docker Hub, tagging it with the short commit hash.

**2. Update Helm Values**

- **Checkout values repository**: Checks out a separate repository containing Helm values.
- **Update `values.yaml`**: Updates the image tag in the `values.yaml` file to the new Docker image tag.
- **Commit and push changes**: Commits and pushes the updated `values.yaml` file back to the repository.

**3. Update Kustomize Values**

- **Checkout values repository**: Checks out a separate repository containing Kustomize configurations.
- **Update `kustomizations`**: Updates the image tag in the Kustomize deployment file to the new Docker image tag.
- **Commit and push changes**: Commits and pushes the updated Kustomize configuration back to the repository.


These are the three jobs we have in the workflow.

![Github Actions workflow](./screenshots/workflow.PNG)


These are the secrets that should be provided to the workflow. 

![Workflow secrets](./screenshots/secrets.PNG)

This is the push history to the dockerhub private repo. 

![DockerHub private repo](./screenshots/dockerhub.PNG)

If you notice, this is the last commit to the wordpress repo which is the same as the latest tag in the dockerhub private repo.

![Github commit ](./screenshots/commit.PNG)



## Configuring ArgoCD to Pull from a Private Repository

To enable ArgoCD to access a private GitHub repository, follow these steps:

### Step 1: Obtain a GitHub Personal Access Token (PAT)

1. Navigate to GitHub and generate a personal access token with the necessary permissions to access your repository.
2. Copy the generated token. You will use it in the next step.

### Step 2: Add the Private Repository to ArgoCD

Use the following command to add your private repository to ArgoCD, replacing `USERNAME` with your GitHub username and `YOUR_PERSONAL_ACCESS_TOKEN` with the PAT you generated:

```sh
argocd repo add https://github.com/Ahmed-Hodhod/ABI_GitOps_K8S --username USERNAME --password YOUR_PERSONAL_ACCESS_TOKEN
```

### Example Command

```sh
argocd repo add https://github.com/Ahmed-Hodhod/ABI_GitOps_K8S --username Ahmed-Hodhod --password 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
```

### Explanation

- **argocd repo add**: Command to add a repository to ArgoCD.
- **Repository URL**: The URL of your private GitHub repository.
- **Username**: Your GitHub username.
- **Password**: The personal access token (PAT) generated from GitHub.

By following these steps, ArgoCD will be configured to securely pull from your specified private repository, facilitating seamless integration and continuous deployment workflows.

This is ArgoCD connected repo.

![Argocd connected Repo](./screenshots/repo.PNG)

## Deploying the Application with Helm

This repository contains a Helm chart for deploying a WordPress application along with a MySQL database on Kubernetes. The chart creates a deployment and service for both WordPress and MySQL, ensuring the application is ready to run with minimal configuration.

### Helm Chart Overview

**Chart Details:**
- **apiVersion**: v2
- **name**: app
- **description**: A Helm chart for Kubernetes
- **type**: application
- **version**: 0.1.0
- **appVersion**: "1.16.0"

### Helm Values

The following values are used to configure the deployment:

**WordPress Configuration:**
```yaml
wordpress:
  image:
    repository: ahmedhodhod1/wordpress
    tag: 2dc8c3a
  service:
    type: NodePort
    port: 80
    nodePort: 30080  # Optional: Define a specific node port
  pvc:
    storage: 20Gi
```

**MySQL Configuration:**
```yaml
mysql:
  rootPassword: password
  database: wordpress
  user: wordpress
  password: password
  pvc:
    storage: 1Gi
```

### Deployment Instructions

1. **Clone the Repository**:
   ```sh
   git clone https://github.com/Ahmed-Hodhod/ABI_GitOps_K8S.git
   cd ABI_GitOps_K8S
   ```

2. **Deploy the Helm Chart**:
   ```sh
   helm install wordpress-app ./app -f ./app/values.yaml
   ```

### Accessing the Application

Once the deployment is complete, you can access the WordPress application using the NodePort specified in the values file. In this example, the application will be accessible on port `30080` of any node in your Kubernetes cluster.


### Repository Link

The Helm chart templates and configuration files can be found in the [ABI_GitOps_K8S repository](https://github.com/Ahmed-Hodhod/ABI_GitOps_K8S).

This is what you should see after running this helm application. 
![HELM Application](./screenshots/helm.PNG)


## Deploying with Kustomize

This project uses Kustomize to manage configurations for deploying the WordPress application in different environments (production and development). Kustomize allows you to customize Kubernetes resource configurations in a declarative manner without modifying the original manifests.

### Kustomize Structure

The Kustomize setup is organized into two main folders:

- **base**: Contains the common manifests for the application.
- **overlays**: Contains environment-specific customizations (patches and `kustomization.yaml` files) for both production and development environments.

### Base Configuration

The `base` folder includes the core manifests for deploying the WordPress application. These manifests are shared across all environments.

### Overlay Configuration

The `overlays` folder contains subfolders for each environment, such as `prod` and `dev`, with specific kustomizations:

- **prod**: Contains kustomizations for the production environment.
- **dev**: Contains kustomizations for the development environment.

Each overlay folder includes a `kustomization.yaml` file and any patches required to modify the base configuration for that specific environment.

### Example: Production Environment

The `kustomization.yaml` file for the production environment references the base configuration and includes specific patches and labels:

```yaml
bases:
- ../../base

commonLabels:
  app: prod

namespace: prod

patchesStrategicMerge: 
  - wordpress_svc.yaml

commonAnnotations:
  app: kustom-prod
```

#### Patch Example

Here is an example of a patch (`wordpress_svc.yaml`) used to customize the WordPress service for the production environment:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  ports:
    - port: 80
      nodePort: 30086
```

### Applying Kustomize Configurations

To deploy the application using Kustomize, you can use the following commands for each environment:

**Production Environment**:
```sh
kubectl apply -k overlays/prod
```

**Development Environment**:
```sh
kubectl apply -k overlays/dev
```

### Repository Structure

The Kustomize files are stored in a separate branch named `Kustomize`. The structure is as follows:

```
kustomize/
  ├── base/
  │   ├── deployment.yaml
  │   ├── service.yaml
  │   └── kustomization.yaml
  ├── overlays/
      ├── prod/
      │   ├── wordpress_svc.yaml
      │   └── kustomization.yaml
      ├── dev/
          ├── wordpress_svc.yaml
          └── kustomization.yaml
```

By using Kustomize, you can easily manage and deploy your application across multiple environments with clear separation and reusable configurations.

This is what you should see after running this kustomize application with argocd. 
![Kustomize Application](./screenshots/kustom.PNG)


### Repository Link

The Kustomize configuration files can be found in the `Kustomize` branch of the [ABI_GitOps_K8S repository](https://github.com/Ahmed-Hodhod/ABI_GitOps_K8S/tree/Kustomize).

 
## Commands Reference

For a comprehensive list of commands used throughout this project, please refer to the [commands.md](commands.md) file. This file contains instructions and commands used to deploy, manage, and configure the WordPress application on Kubernetes, using Helm, Kustomize, and ArgoCD. 