### Project Objectives

- Create a cluster on your local machine using minikube 
- Create a private repo on DockerHub called “wordpress”
- Create a github repo called “wordpress-container”
- Push the wordpress code to your repo (copy the wordpress code from its official repo)
- Create a dockerfile for wordpress and configure the workflow to build and push the image to dockerhub‘s private repo
- Install argocd on your cluster 
- Create a helm chart for your project 
- Create a Kustomize application for your project 
- configure your argocd to deploy automatically when the image version changes in your helm values
- Configure Argocd to pull from a private registry 

### Tools and Technologies 

- Minikube 
- Github Codespace
- Kubernetes 
- Docker 
- Helm Charts 
- Kustomize 
- Argocd 
- Ubuntu 

## GitHub Actions Workflow

This GitHub Actions workflow automates the process of building a Docker image from the repository, pushing it to Docker Hub, and updating the corresponding values in Helm and Kustomize configuration files. The workflow is triggered by a push event to the `master` branch.

### Workflow Overview

**1. Build and Push Docker Image **

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

By following these steps, you can easily deploy and manage your WordPress application on Kubernetes using Helm and ArgoCD.

### Installation

### Using Kustomize 

### Acknowledgement 
