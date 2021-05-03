![image](https://user-images.githubusercontent.com/44756128/116881880-6aa72280-abe9-11eb-9d2f-e089857d4669.png)

# Introduction
We'll go through installing and configuring Terraform version 13 on a Linux OS. We will also explore how to select and use a Terraform provider from among the many providers available publicly.

# Download And Manually Install the Terraform Binary
Download the appropriate Terraform binary package for the provided lab server VM (Linux 64-bit) using the wget command:
```sh
wget -c https://releases.hashicorp.com/terraform/0.13.4/terraform_0.13.4_linux_amd64.zip
```

Unzip the downloaded zip file:
```sh
unzip terraform_0.13.4_linux_amd64.zip
```

Place the Terraform binary in the PATH of the VM operating system so the binary is accessible system-wide to all users:
```sh
sudo mv terraform /usr/sbin/
```


Check the Terraform version information:
```sh
terraform version
```

![image](https://user-images.githubusercontent.com/44756128/116882669-544d9680-abea-11eb-9121-ec6f727251ff.png)

Since the Terraform version is returned, you have validated that the Terraform binary is installed and working properly.

# Clone Over Code for Terraform Providers
Clone the required code from the provided repository:
```sh
git clone https://github.com/linuxacademy/content-hashicorp-certified-terraform-associate-foundations.git
```

If the instance doesn't have git installed, you may use:
```sh
sudo yum install git
```

Switch to the directory where the code is located:
```sh
cd content-hashicorp-certified-terraform-associate-foundations/section3-hol1/
```

View the code in the main.tf file:
```sh
vim main.tf
```

![image](https://user-images.githubusercontent.com/44756128/116882906-97a80500-abea-11eb-91b4-d14cd21193c7.png)

There are two providers and two resources included in this code snippet. The two providers are multiple instances of AWS in different regions. The two resources that will be created and deployed correspond to the simple notification service (SNS) in AWS and are aligned with two separate AWS regions.

Press Escape and enter :q! to exit the file.

# Deploy the Code with Terraform Apply
Enable verbose output logging for Terraform commands using TF_LOG=TRACE:
```sh
export TF_LOG=TRACE
```

Note: You can turn off verbose logging at any time using the export TF_LOG= command.

Initialize the working directory where the code is located:
```sh
terraform init
```

![image](https://user-images.githubusercontent.com/44756128/116883101-d473fc00-abea-11eb-9cb7-f0cd59659cd9.png)

![image](https://user-images.githubusercontent.com/44756128/116883130-df2e9100-abea-11eb-8b7d-991310f0c45e.png)

Notice that Terraform reaches out to the Terraform registry to check the version and fetch the required AWS providers.

Review the actions that will be performed when you deploy the Terraform code:
```sh
terraform plan
```

![image](https://user-images.githubusercontent.com/44756128/116883255-01c0aa00-abeb-11eb-9c41-60a1a7048e3e.png)

![image](https://user-images.githubusercontent.com/44756128/116883289-10a75c80-abeb-11eb-9ea4-d723c908af65.png)

Notice that two resources will be created, consistent with the providers that were configured in the provided code snippet.

Deploy the code:
```sh
terraform apply
```

![image](https://user-images.githubusercontent.com/44756128/116883444-39c7ed00-abeb-11eb-9673-977f217d3a28.png)

When prompted, type yes and press Enter.

Verify that two resources were created with their corresponding Amazon Resource Name (ARN) IDs that includes the region in which the resource was spun up, which validates that the providers worked as intended.

Optionally, verify that the resources were created in their respective regions within the AWS Management Console:
  - Navigate to the AWS Management Console in your browser.
  - Verify that you are logged in to the us-east-1 region upon signing in.
  - Click Services.
  - Type SNS in the search bar and select Simple Notification Service from the contextual menu.
  - In the menu on the left, click Topics.
  - Verify that the topic-us-east resource appears in the list.
  - Click on N. Virginia (the us-east-1 region) at the top-right to engage the Region drop-down, and select us-west-2.
  - Verify that the topic-us-west resource appears in the list.

![image](https://user-images.githubusercontent.com/44756128/116883574-5fed8d00-abeb-11eb-8997-0c306ac5643d.png)

![image](https://user-images.githubusercontent.com/44756128/116883624-6d0a7c00-abeb-11eb-9e39-7b40d031ff51.png)

Tear down the infrastructure you just created before moving on:
```sh
terraform destroy -auto-approve
```

![image](https://user-images.githubusercontent.com/44756128/116883814-a0e5a180-abeb-11eb-813a-69f9e53bc00f.png)
