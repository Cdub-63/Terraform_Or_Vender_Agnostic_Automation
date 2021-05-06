![image](https://user-images.githubusercontent.com/44756128/117312037-5e6dd000-ae4a-11eb-9e75-b2d432ed8a19.png)

# Introduction
Terraform offers a strong set of features to help optimize your Terraform code. Two really useful features are dynamic blocks, which allow you to generate static repeated blocks within resources in Terraform; and built-in functions, which help you manipulate variables and data to suit your needs and help make your Terraform deployments better automated and more fault resilient.

# Check Terraform Status
Check the Terraform status using the version command:
```sh
terraform version
```

![image](https://user-images.githubusercontent.com/44756128/117313463-890c5880-ae4b-11eb-87f3-896668449b52.png)

Since the Terraform version is returned, you have validated that the Terraform binary is installed and functioning properly.

# Clone Terraform Code and Switch to Proper Directory
The Terraform code required for this lab has already been cloned onto the provided VM. Switch to the directory where the code is located:
```sh
cd lab_code
cd section7-HoL-TF-DynBlocks-Funcs
```

List the files in the directory:
```sh
ls
```

![image](https://user-images.githubusercontent.com/44756128/117313982-fa4c0b80-ae4b-11eb-8f93-9b9de678a2c1.png)

The files in the directory should include main.tf, outputs.tf, script.sh, and variables.tf.

# Examine the Code in the Files
View the contents of the main.tf file using the less command:
```sh
less main.tf
```

![image](https://user-images.githubusercontent.com/44756128/117314065-09cb5480-ae4c-11eb-9ceb-311d4117a7f6.png)

The main.tf file spins up AWS networking components such as a virtual private cloud (VPC), security group, internet gateway, route tables, and an EC2 instance bootstrapped with an Apache webserver which is publicly accessible.

Closely examine the code and note the following:
  - We have selected AWS as our provider and our resources will be deployed in the us-east-1 region.
  - We are using the ssm_parameter public endpoint resource to get the AMI ID of the Amazon Linux 2 image that will spin up the EC2 webserver.
  - We are using the vpc module (provided by the Terraform Public Registry) to create our network components like subnets, internet gateway, and route tables.
  - For the security_group resource, we are using a dynamic block on the ingress attribute to dynamically generate as many ingress blocks as we need. The dynamic block includes the var.rules complex variable configured in the variables.tf file.
  - We are also using a couple of built-in functions and some logical expressions in the code to get it to work the way we want, including the join function for the name attribute in the security group resource, and the fileexists and file functions for the user_data parameter in the EC2 instance resource.

Press Escape and enter :q! to exit the file.

View the contents of the variables.tf file:
```sh
less variables.tf
```

![image](https://user-images.githubusercontent.com/44756128/117314319-4303c480-ae4c-11eb-9856-e8a864b7d0cb.png)

The variables.tf file contains the complex variable type which we will be iterating over with the dynamic block in the main.tf file.

Press Escape and enter :q! to exit the file.

View the contents of the script.sh file using the cat command:
```sh
cat script.sh
```

![image](https://user-images.githubusercontent.com/44756128/117314406-5878ee80-ae4c-11eb-9a8d-3e4f1525a71e.png)

The script.sh file is passed into the EC2 instance using its user_data attribute and the fileexists and file functions (as you saw in the main.tf file), which then installs the Apache webserver and starts up the service.

View the contents of the outputs.tf file:
```sh
cat outputs.tf
```

![image](https://user-images.githubusercontent.com/44756128/117314474-6cbceb80-ae4c-11eb-9099-7c0dc3eed0c4.png)

The outputs.tf file returns the values we have requested upon deployment of our Terraform code.
  - The Web-Server-URL output is the publicly accessible URL for our webserver. Notice here that we are using the join function for the value parameter to generate the URL for the webserver.
  - The Time-Date output is the timestamp when we executed our Terraform code.

# Review and Deploy the Terraform Code
As a best practice, format the code in preparation for deployment:
```sh
terraform fmt
```

Initialize the working directory and download the required providers:
```sh
terraform init
```

Validate the code to look for any errors in syntax, parameters, or attributes within Terraform resources that may prevent it from deploying correctly:
```sh
terraform validate
```

![image](https://user-images.githubusercontent.com/44756128/117314657-98d86c80-ae4c-11eb-833f-b795813c51d8.png)

You should receive a notification that the configuration is valid.

Review the actions that will be performed when you deploy the Terraform code:
```sh
terraform plan
```

![image](https://user-images.githubusercontent.com/44756128/117314747-ad1c6980-ae4c-11eb-8aa4-9c1be9340848.png)

Note the Change to Outputs, where you can see the Time-Date and Web-Server-URL outputs that were configured in the outputs.tf file earlier.

Deploy the code:
```sh
terraform apply --auto-approve
```

![image](https://user-images.githubusercontent.com/44756128/117315006-e9e86080-ae4c-11eb-8c0f-629b56583385.png)

Note: The --auto-approve flag will prevent Terraform from prompting you to enter yes explicitly before it deploys the code.

# Test Out the Deployment and Clean Up
Once the code has executed successfully, view the outputs at the end of the completion message:
  - The Time-Date output displays the timestamp when the code was executed.
  - The Web-Server-URL output displays the web address for the Apache webserver we created during deployment.

Note: You could also use the terraform output command at any time in the CLI to view these outputs on demand.

![image](https://user-images.githubusercontent.com/44756128/117315075-f79de600-ae4c-11eb-943d-b37548e6d85c.png)

Verify that the resources were created correctly in the AWS Management Console:
  - Navigate to the AWS Management Console in your browser.
  - Type VPC in the search bar and select VPC from the contextual menu.
  - On the Resources by Region page, click VPCs.
  - Verify that the my-vpc resource appears in the list.

![image](https://user-images.githubusercontent.com/44756128/117315251-21efa380-ae4d-11eb-89ab-d2c834f15e57.png)

  - Type EC2 in the search bar and select EC2 from the contextual menu.
  - On the Resources page, click Instances (running).
  - Verify that the instance, which has no name, appears in the list (and is likely still initializing).

![image](https://user-images.githubusercontent.com/44756128/117315328-37fd6400-ae4d-11eb-934f-dc250077d29a.png)

  - In the menu on the left, click Security Groups.
  - Verify that the Terraform-Dynamic-SG security group appears in the list.
  - Select the security group to see further details.
  - Click on the Inbound rules tab, and note that three separate rules were created from the single dynamic block used on the ingress parameter in the code.

![image](https://user-images.githubusercontent.com/44756128/117315453-54999c00-ae4d-11eb-8a3d-8592a81b9c72.png)

In the CLI, copy the URL displayed as the Web-Server_URL output value.

In a new browser window or tab, paste the URL and press Enter.

Verify that the Apache Test Page loads, validating that the code executed correctly and the logic within the AWS instance in Terraform worked correctly, as it was able to locate the script.sh file in the folder and bootstrap the EC2 instance accordingly.

![image](https://user-images.githubusercontent.com/44756128/117315567-6da24d00-ae4d-11eb-888f-cd5d619ba3c6.png)

In the CLI, tear down the infrastructure you just created before moving on:
```sh
terraform destroy --auto-approve
```

![image](https://user-images.githubusercontent.com/44756128/117315749-988ca100-ae4d-11eb-861b-1b2056488fe5.png)
