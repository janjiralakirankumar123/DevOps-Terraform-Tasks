# DevOps and Terraform Essentials Assignment Tasks

**Lab Instructions:**

1. Perform the Tasks on your own, Based on your Knowledge.
2. It's Mandatory to Upload the Final Screenshots for Every Task.

**Table of Contents:**

1. [Task 1: Creating an EC2 Instance with Apache Web Server using Terraform](#task-1-launching-an-ec2-instance-with-apache-web-server-using-terraform)
2. [Task 2: Installing Git onto the Server and Performing Git Operations](#task-2-installing-git-onto-the-server-and-performing-git-operations)
3. [Task 3: Pushing the Java Code to GitHub Repository](#task-3-pushing-the-java-code-to-github-repository)
4. [Task 4: Configuring Jenkins and Integrating GitHub Repository](#task-4-configuring-jenkins-and-integrating-github-repository)

Let's go through each task in detail:

## Task 1: Launching an EC2 Instance with Apache Web Server using Terraform

### Task 1.1: Manually Launch One EC2 Instance in AWS and Install Terraform on It

1. **Follow the Specific Configurations.:**

    - Log in to AWS Console:
    - **Region:** Choose `North Virginia (us-east-1)` as your AWS region.
    - **Instance Name and Tags:** Give your instance a name like `"YourName-DevOps-Server."`
    - **Application and OS Images (Amazon Machine Image):** Select `Ubuntu-22.04` as the operating system.
    - **Instance Type:** Pick `"t2.micro"` as it's part of the free tier.
    - Click on `"Create a New KeyPair"` and give the Key pair name as `YourName-DevOps-KeyPair`
    - **Network Settings:** `Edit` >> In "Security group name" Enter `"YourName-DevOps-SecurityGroup` and allow ports `22 (SSH),` `80 (HTTP),` and `8080 (Custom)` for web traffic.
    - **Configure storage:** Set storage to `10 GiB.`
    - Click on `Launch Instance.`

2. **SSH into Your EC2 Instance:**

    - Use an SSH client `(like PuTTY or MobaXterm)` and the `KeyPair` you created.
    - Username as `ubuntu` 

3. **Install Terraform:**
  
    ```shell
    sudo hostnamectl set-hostname DevOps-Server
    bash
    ```
    ```shell
    sudo apt update
    ```
    ```shell
    sudo apt install wget unzip -y
    ```
    ```shell
    wget https://releases.hashicorp.com/terraform/1.5.7/terraform_1.5.7_linux_amd64.zip
    ```
    ```shell
    unzip terraform_1.5.7_linux_amd64.zip
    ```
    ```shell
    rm terraform_1.5.7_linux_amd64.zip
    ```
    ```shell
    sudo mv terraform /usr/local/bin
    ```
    ```shell
    terraform
    ```
    ```shell
    terraform -v
    ```

### Task 1.2: Install Required Packages.

- Install Python package manager (pip) and AWS CLI:

```shell
sudo apt-get install python3-pip -y
```

```shell
sudo pip3 install awscli
```

### Task 1.3: Configure AWS CLI for AWS Account Access

**To configure AWS CLI, please follow these steps::**

1.  Type `aws configure` and press enter.
2.  Provide `AWS Access Key ID`, `AWS Secret Access Key`.
3.  Press Enter for `default region name (us-east-1)`, and
4.  Press Enter for `default output format (json)`.

```shell
aws configure
```

### Note: If Credentials are not available, generate them from AWS IAM Service.

Once logged in, check your account access in the terminal with the following command:

```shell
aws s3 ls
```

or

```shell
aws iam list-users
```

Now you are authenticated to create resources in your AWS account.

### Task 1.4: Creating Directory & Terraform Configuration File

1. **Create a Directory:**

```shell
mkdir Terraform_Directory && cd Terraform_Directory
```

2. **Create `ec2_instance.tf` File:**

```shell
vi ec2_instance.tf
```

**Note:**
1. Replace with your desired AWS `Region` & `AMI Id.` (**Default:** `North Virginia (us-east-1)`).
2. `your-key-pair-name` Replace with your existing key pair name (DevOps-KeyPair).
3. Name = `"yourname-TerraformInstance"` that your instance should use.

```hcl
provider "aws" {
  profile = "default"
  region  = "us-east-1"  # Replace with your desired AWS region
}

resource "aws_instance" "web_server" {
  ami           = "ami-053b0d53c279acc90" # Ubuntu 20.04 LTS AMI ID
  instance_type = "t2.micro"
  key_name      = "your-key-pair-name"  # Replace with your existing key pair name

  user_data = <<-EOF
              #!/bin/bash
              sudo apt-get update -y
              sudo apt-get install apache2 -y
              sudo systemctl start apache2
              sudo systemctl enable apache2
              EOF

  tags = {
    Name = "yourname-TerraformInstance"
  }
}
```

### Task 1.5: Resource Provisioning

After creating the `ec2_instance.tf` file, execute the following Terraform commands:

1. **Initialize Terraform:**

```shell
terraform init
```

2. **Format Configuration Files:** Automatically format your Terraform configuration files.

```shell
terraform fmt
```

3. **Validate Configuration:** Check for syntax errors and other issues in your configuration.

```shell
terraform validate
```

4. **Generate Execution Plan:** Creates an execution plan.

```shell
terraform plan
```

5. **Apply Configuration:** Creating AWS resources.

```shell
terraform apply -auto-approve
```

### Task 1.6: Access EC2 Instance and Verify Apache Server's Status

Once the Terraform apply completes successfully, verify the below:

1. **SSH into Newly created EC2 Instance by Terraform:** 

Open **powershell** or **Command Prompt** and enter below command to connect.

```shell
ssh -i <"KeyPair Name"> ubuntu@public-ip-address
```

**Note:** Replace with `<"KeyPair Name">` created in Task-1 and `public-ip-address` with public IP of New instance.

**Example:** `ssh -i "CloudThat-DevOps-KeyPair.pem" ubuntu@172.16.35.247`

2. **Once connected, Check the Apache Server's status (In CLI):**

```shell
sudo systemctl status apache2
```

#### `Once Done Take the Screenshot and Upload it, and Exit from New EC2 Terminal.`

3. **Verify Apache Web Page:**

   - In a web browser, enter your EC2 instance's public IP address and press enter to verify that the Apache web server is installed and running.

#### `Once Done Take the Screenshot and Upload it, then Close the browser`

### Task 1.7: Destroying the Newly Created EC2 Instance.

1. **Destroy Resources:** Navigate to the Terraform directory where your configuration file is located:

```shell
cd ~
cd /home/ubuntu/Terraform_Directory
```

- Run "terraform destroy -auto-approve" to destroy the EC2 instance and associated resources.

```shell
terraform destroy -auto-approve
```

- Once Destroyed, remove the "Terraform_Directory."

```shell
cd ~
rm -rf Terraform_Directory
```

2. **Verify Deletion:**

   - After the destruction, check the AWS console to ensure that the EC2 instance is deleted.

---

## Task 2: Installing Git onto the Server and Performing Git Operations

### Task 2.1: SSH into EC2 Instance and Install Git (Not required if it is already connected)

1. **SSH into EC2 Instance Created earlier (Optional):**

```shell
ssh -i <"KeyPair Name"> ubuntu@public-ip-address
```

2. **Update Packages:**

```shell
cd ~
sudo apt-get update
```

3. **Install "Wget" & "Unzip."**

```shell
sudo apt install wget unzip -y
```

4. **Install Git using the apt package manager:**

```shell
sudo apt-get install git -y
```

5. **Check the git installation status & Version:**

```shell
git --version
```

### Task 2.2: Configure Git with your "name" and "email"

1. To perform further operations, configure your **username** and **email address** by using the following commands::

```shell
git config --global user.name "Your Name"
```

```shell
git config --global user.email "youremail@example.com"
```

### Task 2.3: Download Java Code and Initialize Git Repository:

1. **To download the Java code, execute the following commands:**

```shell
wget https://devops-e-e.s3.ap-south-1.amazonaws.com/hello-world-master.zip
```

```shell
unzip hello-world-master.zip
```

```shell
rm hello-world-master.zip
```

```shell
cd hello-world-master
```

2. **Now, initialize a Git repository with the following command:**

```shell
git init
```
To determine whether files are in a **"tracked"** or **"untracked"** state within Git.

```shell
git status
```

### Task 2.4: Now, Perform Git Operations like `adding files,` `committing changes,` and `checking the Git status`

1. **Add Files to Staging Area:**

```shell
git add .
```
Now files are moved from **"untracked"** to **"tracked"** state.

```shell
git status
```

2. **Commit Changes with a descriptive message:**

```shell
git commit -m "This is the Initial commit"
```
Now, Files will be moved from staging area to Local Repository.

3. **Check Git Status & Logs:**

```shell
git status
```

```shell
git log
```

#### `Once Done Take the Screenshot of "Status" and "Logs" and Upload it`

---

## Task 3: Pushing the Java Code to GitHub Repository

### Task 3.1: Creating GitHub Account and Repository:

   - Create a GitHub account and a new public repository named `hello-world`
   - For Account Creation [SignUp Here](https://github.com/signup) (If its  Not Created)

### Task 3.2: Generating a Personal Access Token (PAT)

To push code to your GitHub repository, you need to generate a Personal Access Token (PAT) for authentication with GitHub.

1. **Steps to Generate a PAT:**

   - Once you login to GitHub.
   - Click on the top right `profile Icon` and then click on `settings.`
   - Click on `Developer settings` (At bottom left menu).
   - Click on `Personal Access Token,` >> `Tokens (classic)` and then Click on `Generate New Token (classic)`
   - Under `Select Scopes (Permissions)` select all items.
   - Click on `generate token.` Then Copy the token and use it

   **PAT Example:** `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`

   **Note:** Save the PAT securely as it will not be accessible again on GitHub.

### Task 3.3: Configuring Local and Remote Repositories to push code

1. **Set Remote Origin (Alias):**

Replace `<GitHub_repository_URL>` with the URL of `hello-world` Repository's Main Branch:

```shell
git remote add origin <GitHub_repository_URL>
```

2. **Verify Remote URL:**

```shell
git remote -v
```

### Task 3.4: Pushing Code to GitHub

```shell
git push origin master
```

**Note:** When it asks for password, enter the **Personal Access Token** and Press Enter
          
`(PAT is invisible, and It's the expected behavior)`

### Task 3.5: Verification

   - Visit your GitHub's `hello-world` repository to confirm that the code has been pushed successfully.

#### `Once verified Take the Screenshot and Upload it`

---

## Task 4: Configuring Jenkins and Integrating GitHub Repository

### Task 4.1: Update the Debian apt repositories, install OpenJDK 17

1. **SSH into EC2 Instance (Not required if already connected):**

2. **Update and Install Java:**

- Update package lists:

```shell
cd ~
sudo apt update
```
- install OpenJDK 17 (OpenJDK is required for running Jenkins.)
   
```shell
sudo apt install openjdk-17-jre
```

3. **Check Java Version:**

```shell
java -version
```

##### Expected Output Example:

*openjdk version "17.0.8.1" 2023-08-24*

*OpenJDK Runtime Environment (build 17.0.8.1+1-Ubuntu-0ubuntu122.04)*

*OpenJDK 64-Bit Server VM (build 17.0.8.1+1-Ubuntu-0ubuntu122.04, mixed mode, sharing)*


### Task 4.2: Jenkins Installation

1. Install Jenkins following the official Jenkins installation instructions for your operating system.

   Click here for [Official Installation Guide](https://www.jenkins.io/doc/book/installing/linux/#debianubuntu).

**(Or)** Directly follow below steps for Installation.

1. **Add Jenkin's Repository:**

```shell
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

```shell
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

```shell
sudo apt-get update
```

2. **Install Jenkins:**

```shell
sudo apt-get install jenkins
```

```shell
sudo apt-get update
```

### Task 4.3: Update, Enable and Start Jenkins:

```shell
sudo systemctl start jenkins
```

```shell
sudo systemctl enable jenkins
```

```shell
sudo systemctl status jenkins
```

### Task 4.4: Unlock Jenkins and Complete Setup


1. **Access the Jenkin's Web Page:**

   - Access the Jenkins web page by opening a web browser and entering your EC2 instance's public IP address followed by port 8080 `(e.g., http://<public-ip>:8080/)`

2. **Unlock Jenkins:**

   - To Unlock the Jenkin's Web Page Retrieve the Jenkin's `initialAdminPassword` from below path using CLI Terminal and paste it:

     ```shell
     sudo cat /var/lib/jenkins/secrets/initialAdminPassword
     ```

### Task 4.5: Installing Plugins and Create Admin User

   - Once Unlocked then Choose the option to `install suggested plugins`
   - Follow the instructions to `Create an admin user` for Jenkins.
   - Confirm the Jenkins URL (it should match your EC2 instance's public IP address and port 8080).
   - Click the "Start using Jenkins" button to access the Jenkins dashboard.

#### Now you will be prompted to the Jenkins Home Page

### Task 4.6: Install any additional plugins required for your project and configuration

1. Click on `Manage Jenkins` > `Plugins` > `Available Plugins` tab and search for `Maven`.
2. Select the `"Maven Integration"` Plugin and click `Install` (without restart).
3. Once the installation is completed, click on `Go back to the top page`.

### Task 4.7: Maven Integration

1. Click on `Manage Jenkins` > `Tool` and look for `Maven installations` Then click on `Add Maven.`
2. Give the Name as `Maven` choose `Version as 3.9.4,` and `Save` the configuration.

### Task 4.8: Create Jenkin's Pipeline

1. Now we need to create a project for building your application, for that select `New Item` and name it as `hello-world` Choose `Maven Project` as the project type and click `OK.`
   
   ( You will be prompted to the **configure page** inside the **hello-world project.**)

3. Scroll down to the "Source Code Management" section, and select `Git'`
4. provide the `hello-world Repository's Master Branch URL`

   (e.g., https://github.com/your-username/hello-world.git).

5. You need to provide GitHub Credentials to create, click on `Add` >> `Jenkins.` then you will be prompted to the `Jenkins Credentials Provider` page. Under `Add Credentials` you can add your `GitHub Username,` `Password,` and `Description.` Then click on `Add.`

6. Once after returning to the SCM Page, click on Credentials and select your `GitHub Credentials.`

7. Keep all the other values as default and go to `"Build"` section and `inside Goals and options` write `clean package` and `save` the configuration.

   **Note:** 'clean package' command **clears the target directory**, **Builds the project**, and **packages** the resulting **WAR file** into the target directory.

8. Now, you will be back to the `"hello-world"` project. Now click on `"Build Now"` for Manually building the `.war` file for your application.

**Note:** `If Build Fails` Go to GitHub's `hello-world repository` >> `pom.xml` and change the Version from `2.1.1` to `3.3.0` and Commit the changes, then On Jenkin's page again click on `Build Now.` now you can find successful build.

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.efsavage</groupId>
	<artifactId>hello-world-war</artifactId>
	<version>1.0.0</version>
	<packaging>war</packaging>
	<name>Hello World Web Application Repository</name>
	<description>Simplest possible Java Webapp</description>

	<build>
		<plugins>
			<plugin>
				<groupId>org.mortbay.jetty</groupId>
				<artifactId>jetty-maven-plugin</artifactId>
				<version>8.1.5.v20120716</version>
				<configuration>
					<scanIntervalSeconds>0</scanIntervalSeconds>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-war-plugin</artifactId>
				<version>2.1.1</version>
			</plugin>
		</plugins>
	</build>

</project>
```

### Task 4.9: Check the resulting .war file,

1. In Jenkin's Web Page you can go to `Workspace` >> `dist` folder to see that the `.war` file is created there.

2. In CLI `war` file will be created in `/var/lib/jenkins/workspace/hello-world/target/` location.

### Task-4.10: Integrating GitWebHook to automatically build your code using Jenkins.
In Jenkin's Web Page

1. Click on `"Manage Jenkins"` >> `"Plugins."` and choose `"Available"` tab.
2. Search and select the following plugins:
    * `GitHub Integration` and click `"Install"` (without restart).

### Task-4.11: Set Up GitHub Webhook
1. Go back to your GitHub's `hello-world` repository in a web browser.
2. Click on the `Settings` tab and then select `Webhooks` from the left-hand side.
4. Click the `"Add webhook"` button.
5. In the `Payload URL` field, enter as the following URL: `http://<jenkins-public-ip>:8080/github-webhook/`
6. Set the `"Content type"` to `application/json`
7. Under `"Which events would you like to trigger this webhook?"` select `"Just the push event"`
8. Click `"Add webhook"` to create the webhook.

### Task-4.12: Check for Automatic Build:
1. Now, Make minor change in `hello.txt` file and commit in GitHub's `hello-world` repository.
2. As the source code gets changed, Jenkin gets triggered by the WebHook and automatically starts building the new source code.
3. To check go to Jenkin's Web Page, and you can see a build is happening and Observe the successful load build.

#### `Once verified Take the Screenshot and Upload it`

#### ================================**END of TASK-04**================================
