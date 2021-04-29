![image](https://user-images.githubusercontent.com/44756128/116551358-78913680-a8bd-11eb-902f-95eb1535b9e4.png)

# Introduction
We will be following the Terraform workflow — Write > Plan > Apply — to deploy a virtual machine (VM) in AWS. After a successful deployment, we will then clean up our infrastructure and destroy the resource we created.

# Create a Directory and Write Your Terraform Code (Write)
In the CLI, create a new directory in the cloud_user's home directory called terraform_code to house your Terraform code:
```sh
mkdir terraform_code
```

Switch to the new directory:
```sh
cd terraform_code
```

Using vi, create a new file called main.tf where you will write your code:
```sh
vi main.tf
```

In the file, paste the provided code that will be used to create the required VM (EC2 instance) in AWS:
```tf
provider "aws" {
  region = "us-east-1"
}
resource "aws_instance" "vm" {
  ami           = "DUMMY_VALUE_AMI_ID"
  subnet_id     = "DUMMY_VALUE_SUBNET_ID"
  instance_type = "t3.micro"
  tags = {
    Name = "my-first-tf-node"
  }
}
```

Press Escape and enter :wq to save and exit the file.

![image](https://user-images.githubusercontent.com/44756128/116552116-4cc28080-a8be-11eb-819f-b6622f1f156f.png)

# Plug the Provided AMI and Subnet ID Values Into Your Code
View the contents of the resource_ids.txt file that has been saved on the lab server:
```sh
cat /home/cloud_user/resource_ids.txt
```

![image](https://user-images.githubusercontent.com/44756128/116552513-b9d61600-a8be-11eb-9ae3-4712b2a77244.png)

The ami and subnet_id values that have been saved in this file will be displayed.

Copy the ami value.

Open the main.tf file that houses your code:
```sh
vi main.tf
```

Paste the ami value into your code for the ami parameter, replacing the DUMMY_VALUE_AMI_ID placeholder text. (Note: You can add the subnet_id value at this step as well if you wish).

Press Escape and enter :wq to save and exit the file.

Copy the subnet_id value.

Open the file with your code again:
```sh
vi main.tf
```

Paste the subnet_id value into your code for the subnet_id parameter, replacing the DUMMY_VALUE_SUBNET_ID placeholder text.

Press Escape and enter :wq to save and exit the file.

![image](https://user-images.githubusercontent.com/44756128/116552542-c35f7e00-a8be-11eb-9ddb-4cff6156805a.png)

# Initialize and Review Your Terraform Code (Plan)
Initialize the Terraform configuration and download the required providers:
```sh
terraform init
```

![image](https://user-images.githubusercontent.com/44756128/116552722-f275ef80-a8be-11eb-8249-ad30a8c9dfb4.png)

Review the actions that will be performed when you deploy your code:
```sh
terraform plan
```

![image](https://user-images.githubusercontent.com/44756128/116552770-00c40b80-a8bf-11eb-8a56-ecff04d2f019.png)

![image](https://user-images.githubusercontent.com/44756128/116552793-09b4dd00-a8bf-11eb-9f7e-82c3ff6717bf.png)

In this case, it will create 1 resource: the EC2 instance you configured in your code.

If you scroll up, you will notice that only the ami, instance_type, subnet_id, and tags properties are configured, as that was included in your code.

Everything else, denoted with a + sign, will be created from scratch or will be populated when Terraform creates the resource upon deployment of your code.

# Deploy Your Terraform Code (Apply), Verify Your Resources, and Clean Up
Deploy the code:
```sh
terraform apply
```

![image](https://user-images.githubusercontent.com/44756128/116553007-48e32e00-a8bf-11eb-8337-781b558a630f.png)

When prompted, type yes and press Enter.

![image](https://user-images.githubusercontent.com/44756128/116552972-3ff25c80-a8bf-11eb-8bce-c2c3525b98dc.png)

Once the code has executed successfully, note in the output that 1 resource has been created.

Note: You could also use the terraform output command at any time in the CLI to view the output on demand.

Verify that the resource was created correctly in the AWS Management Console:
  - Navigate to the AWS Management Console in your browser.
  - Type EC2 in the search bar and select EC2 from the contextual menu.
  - On the Resources page, click Instances (running).
  - Verify that the instance, named my-first-tf-node (as configured in your code), appears in the list.

![image](https://user-images.githubusercontent.com/44756128/116553109-62847580-a8bf-11eb-93da-6ee4afffe22e.png)

Back in the CLI, remove the infrastructure you just created:
```sh
terraform destroy
```

![image](https://user-images.githubusercontent.com/44756128/116553214-821b9e00-a8bf-11eb-8a74-c534f095bf74.png)

In the plan output, notice that it will destroy 1 resource: the EC2 instance you just created.

Note: You can also scroll through the rest of the plan output and view the properties of the resource that will be destroyed, if desired.

When prompted, type yes and press Enter.

In the notifications displayed in the CLI, note that the aws_instance.vm resource you created is now being destroyed.

![image](https://user-images.githubusercontent.com/44756128/116553292-965f9b00-a8bf-11eb-9065-175f79941808.png)

In the AWS Management Console, click the refresh button inside the Instances page and verify that the my-first-tf-node instance no longer appears in the list.

![image](https://user-images.githubusercontent.com/44756128/116553345-a4152080-a8bf-11eb-822c-12a90357d15d.png)
