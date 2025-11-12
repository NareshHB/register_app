# INTRODUCTION

This project leverages a variety of tools and technologies to create an end-to-end pipeline, starting from codecommit to deployment and monitoring. By incorporating DevSecOps practices, we ensure that security is a key component at every stage of the pipeline. Key tools used in this project include Jenkins for orchestration, Maven for build management, SonarQube for code quality checks, Trivy for security scans, Nexus Repository for artifact management, Docker for containerization, Kubernetes for deployment, and Prometheus and Grafana for monitoring.

<img width="938" height="637" alt="image" src="https://github.com/user-attachments/assets/7d3e4b68-467c-4bc9-8020-e692c7211d8e" />

**The architecture of this CI/CD pipeline integrates various tools and technologies to facilitate seamless development, testing, deployment, and monitoring. Here's a condensed overview of the architecture and tools used:**

1. **GitHub:** Source code repository where developers push their code changes. [https://github.com/NareshHB/register_app]
2. **Jenkins:** CI/CD orchestrator that automates the building, testing, and deployment of the application.
3. **Maven:** Used for compiling the code, running unit tests, and packaging the application.
4. **SonarQube:** Performs static code analysis to identify code smells, bugs, and security vulnerabilities.
5. **Trivy:** Scans the file system and Docker images for security vulnerabilities.
6. **Nexus:** Repository: Stores the built artifacts (JAR files) for managing and distributing the build outputs.
7. **Docker:** Containerizes the application to ensure it runs consistently across different environments.
8. **Kubernetes:** (EKS on AWS): Orchestrates the deployment, management, and scaling of the containerized applications.
9. **Prometheus and Grafana:** Collects metrics and provides visualization dashboards for continuous monitoring and alerting of the system's health and performance.

# Pipeline Workflow
**The pipeline consists of multiple stages to ensure code quality, security, and successful deployment:**

1. Git Checkout: Jenkins checks out the code from the GitHub repository.

2. Compile: Maven compiles the code.

3. Test: Maven runs unit tests.

4. File System Scan: Trivy scans the file system for vulnerabilities.

5. SonarCloud Analysis: SonarQube performs code quality analysis.

6. Quality Gate: Jenkins waits for SonarQube’s quality gate results.

7. Build: Maven packages the application.

8. Publish to Nexus: Jenkins publishes the build artifacts to the Nexus Repository.

9. Build & Tag Docker Image: Docker builds and tags the application image.

10. Docker Image Scan: Trivy scans the Docker image for vulnerabilities.

11. Push Docker Image: Jenkins pushes the Docker image to the registry.

12. Deploy to Kubernetes: Jenkins deploys the application to the Kubernetes cluster.

13. Verify the Deployment: Jenkins verifies the deployment by checking the pods and services.

## Steps to create a private Git repository, generate a personal access token, connect to the repository, and push code to it:

1. **Create a Private Git Repository**:
   - Go to your preferred Git hosting platform (e.g., GitHub, GitLab, Bitbucket).
   - Log in to your account or sign up if you don't have one.
   - Create a new repository and set it as private.

2. **Generate a Personal Access Token**:
   - Navigate to your account settings or profile settings.
   - Look for the "Developer settings" or "Personal access tokens" section.
   - Generate a new token, providing it with the necessary permissions (e.g., repo access).

3. **Clone the Repository Locally**:
   - Open Git Bash or your terminal.
   - Navigate to the directory where you want to clone the repository.
   - Use the `git clone` command followed by the repository's URL. For example:
     ```bash
     git clone <repository_URL>
     ```
   Replace `<repository_URL>` with the URL of your private repository.

4. **Add Your Source Code Files**:
   - Navigate into the cloned repository directory.
   - Paste your source code files or create new ones inside this directory.

5. **Stage and Commit Changes**:
   - Use the `git add` command to stage the changes:
     ```bash
     git add .
     ```
   - Use the `git commit` command to commit the staged changes along with a meaningful message:
     ```bash
     git commit -m "Your commit message here"
     ```

6. **Push Changes to the Repository**:
   - Use the `git push` command to push your committed changes to the remote repository:
     ```bash
     git push
     ```
   - If it's your first time pushing to this repository, you might need to specify the remote and branch:
     ```bash
     git push -u origin master
     ```
   Replace `master` with the branch name if you're pushing to a different branch.

7. **Enter Personal Access Token as Authentication**:
   - When prompted for credentials during the push, enter your username (usually your email) and use your personal access token as the password.

By following these steps, you'll be able to create a private Git repository, connect to it using Git Bash, and push your code changes securely using a personal access token for authentication.

## First Create a user in AWS IAM with any name
**Attach Policies to the newly created user**
**Below policies**

AmazonEC2FullAccess

AmazonEKS_CNI_Policy

AmazonEKSClusterPolicy	

AmazonEKSWorkerNodePolicy

AWSCloudFormationFullAccess

IAMFullAccess

#### One more policy we need to create with content as below
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "eks:*",
            "Resource": "*"
        }
    ]
}
```
Attach this policy to your user as well

![Policies To Attach](https://github.com/jaiswaladi246/EKS-Complete/blob/main/Policies.png)

# AWSCLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
aws configure
```

## KUBECTL

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

## EKSCTL

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

## Create EKS CLUSTER

```bash
eksctl create cluster --name=my-eks22 \
                      --region=ap-south-1 \
                      --zones=ap-south-1a,ap-south-1b \
                      --version=1.30 \
                      --without-nodegroup

eksctl utils associate-iam-oidc-provider \
    --region ap-south-1 \
    --cluster my-eks22 \
    --approve

eksctl create nodegroup --cluster=my-eks22 \
                       --region=ap-south-1 \
                       --name=node2 \
                       --node-type=t3.medium \
                       --nodes=3 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=Key \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access
```

* Open INBOUND TRAFFIC IN ADDITIONAL Security Group
* Create Servcie account/ROLE/BIND-ROLE/Token

## Create Service Account, Role & Assign that role, And create a secret for Service Account and geenrate a Token

### Creating Service Account


```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
```

### Create Role 


```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - secrets
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

### Bind the role to service account


```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps 
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role 
subjects:
- namespace: webapps 
  kind: ServiceAccount
  name: jenkins 
```

### Generate token using service account in the namespace

[Create Token](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#:~:text=To%20create%20a%20non%2Dexpiring,with%20that%20generated%20token%20data.)





