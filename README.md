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

SetUp Nexus
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
Save this script in a file, for example, install_docker.sh, and make it executable using:

chmod +x install_docker.sh
Then, you can run the script using:

./install_docker.sh
Create Nexus using docker container
To create a Docker container running Nexus 3 and exposing it on port 8081, you can use the following command:

docker run -d --name nexus -p 8081:8081 sonatype/nexus3:latest
This command does the following:

-d: Detaches the container and runs it in the background.
--name nexus: Specifies the name of the container as "nexus".
-p 8081:8081: Maps port 8081 on the host to port 8081 on the container, allowing access to Nexus through port 8081.
sonatype/nexus3:latest: Specifies the Docker image to use for the container, in this case, the latest version of Nexus 3 from the Sonatype repository.
After running this command, Nexus will be accessible on your host machine at http://IP:8081.

Get Nexus initial password
Your provided commands are correct for accessing the Nexus password stored in the container. Here's a breakdown of the steps:

Get Container ID: You need to find out the ID of the Nexus container. You can do this by running:

docker ps
This command lists all running containers along with their IDs, among other information.

Access Container's Bash Shell: Once you have the container ID, you can execute the docker exec command to access the container's bash shell:

docker exec -it <container_ID> /bin/bash
Replace <container_ID> with the actual ID of the Nexus container.

Navigate to Nexus Directory: Inside the container's bash shell, navigate to the directory where Nexus stores its configuration:

cd sonatype-work/nexus3
View Admin Password: Finally, you can view the admin password by displaying the contents of the admin.password file:

cat admin.password
Exit the Container Shell: Once you have retrieved the password, you can exit the container's bash shell:

exit
This process allows you to access the Nexus admin password stored within the container. Make sure to keep this password secure, as it grants administrative access to your Nexus instance.
