To create an Ubuntu EC2 instance in AWS, follow these steps:

1. Sign in to the AWS Management Console:

Go to the AWS Management Console at https://aws.amazon.com/console/.
Sign in with your AWS account credentials.

2. Navigate to EC2:

Once logged in, navigate to the EC2 dashboard by typing "EC2" in the search bar at the top or by selecting "Services" and then "EC2" under the "Compute" section.

3. Launch Instance:

Click on the "Instances" link in the EC2 dashboard sidebar.
Click the "Launch Instance" button.

4. Choose an Amazon Machine Image (AMI):

In the "Step 1: Choose an Amazon Machine Image (AMI)" section, select "Ubuntu" from the list of available AMIs.
Choose the Ubuntu version you want to use. For example, "Ubuntu Server 20.04 LTS".
Click "Select".

5. Choose an Instance Type:

In the "Step 2: Choose an Instance Type" section, select the instance type that fits your requirements. The default option (usually a t2.micro instance) is suitable for testing and small workloads.
Click "Next: Configure Instance Details".

6.Configure Instance Details:

Optionally, configure instance details such as network settings, subnets, IAM role, etc. You can leave these settings as default for now.
Click "Next: Add Storage".

7.Add Storage:

Specify the size of the root volume (default is usually fine for testing purposes).
Click "Next: Add Tags".

8.Add Tags:

Optionally, add tags to your instance for better organization and management.
Click "Next: Configure Security Group".

9.Configure Security Group:

In the "Step 6: Configure Security Group" section, configure the security group to allow SSH access (port 22) from your IP address.
You may also want to allow other ports based on your requirements (e.g., HTTP, HTTPS) as in this pic 

<img width="863" height="390" alt="image" src="https://github.com/user-attachments/assets/b26c4aaa-1d9a-4fb8-8516-749d8765188e" />



Click "Review and Launch".

10.Review and Launch:

Review the configuration of your instance.
Click "Launch".

11.Select Key Pair:

In the pop-up window, select an existing key pair or create a new one.
Check the acknowledgment box.
Click "Launch Instances".

12.Access Your Instance:

Use Mobaxterm

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Installing Jenkins on Ubuntu


```bash
#!/bin/bash

# Install OpenJDK 17 JRE Headless
sudo apt install openjdk-17-jre-headless -y

# Download Jenkins GPG key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# Add Jenkins repository to package manager sources
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update package manager repositories
sudo apt-get update

# Install Jenkins
sudo apt-get install jenkins -y
```

Save this script in a file, for example, `install_jenkins.sh`, and make it executable using:

```bash
chmod +x install_jenkins.sh
```

Then, you can run the script using:

```bash
./install_jenkins.sh
```

This script will automate the installation process of OpenJDK 17 JRE Headless and Jenkins.


## Install docker for future use

```bash
#!/bin/bash

# Update package manager repositories
sudo apt-get update

# Install necessary dependencies
sudo apt-get install -y ca-certificates curl

# Create directory for Docker GPG key
sudo install -m 0755 -d /etc/apt/keyrings

# Download Docker's GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

# Ensure proper permissions for the key
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repository to Apt sources
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update package manager repositories
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin 
```

Save this script in a file, for example, `install_docker.sh`, and make it executable using:

```bash
chmod +x install_docker.sh
```

Then, you can run the script using:

```bash
./install_docker.sh
```
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# SetUp Nexus

```bash
#!/bin/bash

# Update package manager repositories
sudo apt-get update

# Install necessary dependencies
sudo apt-get install -y ca-certificates curl

# Create directory for Docker GPG key
sudo install -m 0755 -d /etc/apt/keyrings

# Download Docker's GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

# Ensure proper permissions for the key
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repository to Apt sources
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update package manager repositories
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin 
```

Save this script in a file, for example, `install_docker.sh`, and make it executable using:

```bash
chmod +x install_docker.sh
```

Then, you can run the script using:

```bash
./install_docker.sh
```

## Create Nexus using docker container

To create a Docker container running Nexus 3 and exposing it on port 8081, you can use the following command:

```bash
docker run -d --name nexus -p 8081:8081 sonatype/nexus3:latest
```

This command does the following:

- `-d`: Detaches the container and runs it in the background.
- `--name nexus`: Specifies the name of the container as "nexus".
- `-p 8081:8081`: Maps port 8081 on the host to port 8081 on the container, allowing access to Nexus through port 8081.
- `sonatype/nexus3:latest`: Specifies the Docker image to use for the container, in this case, the latest version of Nexus 3 from the Sonatype repository.

After running this command, Nexus will be accessible on your host machine at http://IP:8081.

## Get Nexus initial password
Your provided commands are correct for accessing the Nexus password stored in the container. Here's a breakdown of the steps:

1. **Get Container ID**: You need to find out the ID of the Nexus container. You can do this by running:

    ```bash
    docker ps
    ```

    This command lists all running containers along with their IDs, among other information.

2. **Access Container's Bash Shell**: Once you have the container ID, you can execute the `docker exec` command to access the container's bash shell:

    ```bash
    docker exec -it <container_ID> /bin/bash
    ```

    Replace `<container_ID>` with the actual ID of the Nexus container.

3. **Navigate to Nexus Directory**: Inside the container's bash shell, navigate to the directory where Nexus stores its configuration:

    ```bash
    cd sonatype-work/nexus3
    ```

4. **View Admin Password**: Finally, you can view the admin password by displaying the contents of the `admin.password` file:

    ```bash
    cat admin.password
    ```

5. **Exit the Container Shell**: Once you have retrieved the password, you can exit the container's bash shell:

    ```bash
    exit
    ```

This process allows you to access the Nexus admin password stored within the container. Make sure to keep this password secure, as it grants administrative access to your Nexus instance.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# SetUp SonarQube

```bash
#!/bin/bash

# Update package manager repositories
sudo apt-get update

# Install necessary dependencies
sudo apt-get install -y ca-certificates curl

# Create directory for Docker GPG key
sudo install -m 0755 -d /etc/apt/keyrings

# Download Docker's GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

# Ensure proper permissions for the key
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repository to Apt sources
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update package manager repositories
sudo apt-get update

sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin 
```

Save this script in a file, for example, `install_docker.sh`, and make it executable using:

```bash
chmod +x install_docker.sh
```

Then, you can run the script using:

```bash
./install_docker.sh
```

## Create Sonarqube Docker container
To run SonarQube in a Docker container with the provided command, you can follow these steps:

1. Open your terminal or command prompt.

2. Run the following command:

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

This command will download the `sonarqube:lts-community` Docker image from Docker Hub if it's not already available locally. Then, it will create a container named "sonar" from this image, running it in detached mode (`-d` flag) and mapping port 9000 on the host machine to port 9000 in the container (`-p 9000:9000` flag).

3. Access SonarQube by opening a web browser and navigating to `http://VmIP:9000`.

This will start the SonarQube server, and you should be able to access it using the provided URL. If you're running Docker on a remote server or a different port, replace `localhost` with the appropriate hostname or IP address and adjust the port accordingly.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

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

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Install AWS Cli on the above EC2
Refer--https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
$ sudo su
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
$ apt install unzip,   $ unzip awscliv2.zip
$ sudo ./aws/install
         OR
$ sudo yum remove -y aws-cli
$ pip3 install --user awscli
$ sudo ln -s $HOME/.local/bin/aws /usr/bin/aws
$ aws --version

## Installing kubectl
Refer--https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html
$ sudo su
$ curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.27.1/2023-04-19/bin/linux/amd64/kubectl
$ ll , $ chmod +x ./kubectl  //Gave executable permisions
$ mv kubectl /bin   //Because all our executable files are in /bin
$ kubectl version --output=yaml

## Installing  eksctl
Refer---https://github.com/eksctl-io/eksctl/blob/main/README.md#installation
$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
$ cd /tmp
$ ll
$ sudo mv /tmp/eksctl /bin
$ eksctl version

## Setup Kubernetes using eksctl
Refer--https://github.com/aws-samples/eks-workshop/issues/734
$ eksctl create cluster --name virtualtechbox-cluster \
--region ap-south-1 \
--node-type t2.small \
--nodes 3 \

$ kubectl get nodes

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


Setup Bootstrap Server for eksctl and Setup Kubernetes using eksctl 
## Install AWS Cli on the above EC2
Refer--https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
$ sudo su
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
$ apt install unzip,   $ unzip awscliv2.zip
$ sudo ./aws/install
         OR
$ sudo yum remove -y aws-cli
$ pip3 install --user awscli
$ sudo ln -s $HOME/.local/bin/aws /usr/bin/aws
$ aws --version

## Installing kubectl
Refer--https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html
$ sudo su
$ curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.27.1/2023-04-19/bin/linux/amd64/kubectl
$ ll , $ chmod +x ./kubectl  //Gave executable permisions
$ mv kubectl /bin   //Because all our executable files are in /bin
$ kubectl version --output=yaml

## Installing  eksctl
Refer---https://github.com/eksctl-io/eksctl/blob/main/README.md#installation
$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
$ cd /tmp
$ ll
$ sudo mv /tmp/eksctl /bin
$ eksctl version

## Setup Kubernetes using eksctl
Refer--https://github.com/aws-samples/eks-workshop/issues/734
$ eksctl create cluster --name virtualtechbox-cluster \
--region ap-south-1 \
--node-type t2.small \
--nodes 3 \

$ kubectl get nodes

============================================================= ArgoCD Installation on EKS Cluster and Add EKS Cluster to ArgoCD ==========================================================
1 ) First, create a namespace
    $ kubectl create namespace argocd

2 ) Next, let's apply the yaml configuration files for ArgoCd
    $ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

3 ) Now we can view the pods created in the ArgoCD namespace.
    $ kubectl get pods -n argocd

4 ) To interact with the API Server we need to deploy the CLI:
    $ curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.4.7/argocd-linux-amd64
    $ chmod +x /usr/local/bin/argocd

5 ) Expose argocd-server
    $ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

6 ) Wait about 2 minutes for the LoadBalancer creation
    $ kubectl get svc -n argocd

7 ) Get pasword and decode it.
    $ kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
    $ echo WXVpLUg2LWxoWjRkSHFmSA== | base64 --decode

## Add EKS Cluster to ArgoCD
9 ) login to ArgoCD from CLI
    $ argocd login a2255bb2bb33f438d9addf8840d294c5-785887595.ap-south-1.elb.amazonaws.com --username admin

10 ) 
     $ argocd cluster list

11 ) Below command will show the EKS cluster
     $ kubectl config get-contexts

12 ) Add above EKS cluster to ArgoCD with below command
     $ argocd cluster add i-08b9d0ff0409f48e7@virtualtechbox-cluster.ap-south-1.eksctl.io --name virtualtechbox-eks-cluster

13 ) $ kubectl get svc
============================================================= Cleanup =============================================================
$ kubectl get all
$ kubectl delete deployment.apps/virtualtechbox-regapp       //it will delete the deployment
$ kubectl delete service/virtualtechbox-service              //it will delete the service
$ eksctl delete cluster virtualtechbox --region ap-south-1     OR    eksctl delete cluster --region=ap-south-1 --name=virtualtechbox-cluster      //it will delete the EKS cluster
