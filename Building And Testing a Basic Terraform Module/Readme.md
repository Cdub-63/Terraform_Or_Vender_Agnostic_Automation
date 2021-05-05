![image](https://user-images.githubusercontent.com/44756128/117154373-657cdb80-ad81-11eb-9eae-9f84f24f4f05.png)

# Introduction
Terraform modules are a good way to abstract out repeated chunks of code, making it reusable across other Terraform projects and configurations. We'll be writing a basic Terraform module from scratch and then testing it out.

# Create the Directory Structure for the Terraform Project
Check the Terraform status using the version command:
```sh
terraform version
```

Since the Terraform version is returned, you have validated that the Terraform binary is installed and functioning properly.

Create a new directory called terraform_project to house your Terraform code:
```sh
mkdir terraform_project
```

Switch to this main project directory:
```sh
cd terraform_project
```

Create a custom directory called modules and a directory inside it called vpc:
```sh
mkdir -p modules/vpc
```

Switch to the vpc directory using the absolute path:
```sh
cd /home/cloud_user/terraform_project/modules/vpc/
```

![image](https://user-images.githubusercontent.com/44756128/117156500-5dbe3680-ad83-11eb-8307-7e99ef1be966.png)

# Write Your Terraform VPC Module Code
Using Vim, create a new file called main.tf:
```sh
vim main.tf
```

In the file, insert and review the provided code:
```tf
provider "aws" {
  region = var.region
}

resource "aws_vpc" "this" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "this" {
  vpc_id     = aws_vpc.this.id
  cidr_block = "10.0.1.0/24"
}

data "aws_ssm_parameter" "this" {
  name = "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
}
```

Press Escape and enter :wq to save and exit the file.

Create a new file called variables.tf:
```sh
vim variables.tf
```

In the file, insert and review the provided code:
```tf
variable "region" {
  type    = string
  default = "us-east-1"
}
```

Press Escape and enter :wq to save and exit the file.

Create a new file called outputs.tf:
```sh
vim outputs.tf
```

In the file, insert and review the provided code:
```tf
output "subnet_id" {
  value = aws_subnet.this.id
}

output "ami_id" {
  value = data.aws_ssm_parameter.this.value
}
```

![image](https://user-images.githubusercontent.com/44756128/117156962-cdccbc80-ad83-11eb-83a8-83ba967fd3bb.png)

Note: The code in outputs.tf is critical to exporting values to your main Terraform code, where you'll be referencing this module. Specifically, it returns the subnet and AMI IDs for your EC2 instance.

Press Escape and enter :wq to save and exit the file.

# Write Your Main Terraform Project Code
Switch to the main project directory:
```sh
cd ~/terraform_project
```

Create a new file called main.tf:
```sh
vim main.tf
```

In the file, insert and review the provided code:
```tf
variable "main_region" {
  type    = string
  default = "us-east-1"
}

provider "aws" {
  region = var.main_region
}

module "vpc" {
  source = "./modules/vpc"
  region = var.main_region
}

resource "aws_instance" "my-instance" {
  ami           = module.vpc.ami_id
  subnet_id     = module.vpc.subnet_id
  instance_type = "t2.micro"
}
```

Note: The code in main.tf invokes the VPC module that you created earlier. Notice how you're referencing the code using the source option within the module block to let Terraform know where the module code resides.

Press Escape and enter :wq to save and exit the file.

Create a new file called outputs.tf:
```sh
vim outputs.tf
```

In the file, insert and review the provided code:
```tf
output "PrivateIP" {
  description = "Private IP of EC2 instance"
  value       = aws_instance.my-instance.private_ip
}
```

![image](https://user-images.githubusercontent.com/44756128/117157399-2f8d2680-ad84-11eb-96ad-742a1085c8b8.png)

Press Escape and enter :wq to save and exit the file.

# Deploy Your Code and Test Out Your Module
Format the code in all of your files in preparation for deployment:
```sh
terraform fmt -recursive
```

Initialize the Terraform configuration to fetch any required providers and get the code being referenced in the module block:
```sh
terraform init
```

Validate the code to look for any errors in syntax, parameters, or attributes within Terraform resources that may prevent it from deploying correctly:
```sh
terraform validate
```

You should receive a notification that the configuration is valid.

Review the actions that will be performed when you deploy the Terraform code:
```sh
terraform plan
```

![image](https://user-images.githubusercontent.com/44756128/117157710-6feca480-ad84-11eb-882f-233d05900a6b.png)

![image](https://user-images.githubusercontent.com/44756128/117157887-97dc0800-ad84-11eb-9e62-a74828d5c41e.png)

In this case, it will create 3 resources, which includes the EC2 instance configured in the root code and any resources configured in the module. If you scroll up and view the resources that will be created, any resource with module.vpc in the name will be created via the module code, such as module.vpc.aws_vpc.this.

Deploy the code:
```sh
terraform apply --auto-approve
```

Note: The --auto-approve flag will prevent Terraform from prompting you to enter yes explicitly before it deploys the code.

Once the code has executed successfully, note in the output that 3 resources have been created and the private IP address of the EC2 instance is returned as was configured in the outputs.tf file in your main project code.

View all of the resources that Terraform has created and is now tracking in the state file:
```sh
terraform state list
```

![image](https://user-images.githubusercontent.com/44756128/117158226-da054980-ad84-11eb-9da9-9eb6035d4f39.png)

![image](https://user-images.githubusercontent.com/44756128/117158551-20f33f00-ad85-11eb-82fa-897ac0a82da8.png)

![image](https://user-images.githubusercontent.com/44756128/117158612-30728800-ad85-11eb-9d49-bfc866268ea9.png)

![image](https://user-images.githubusercontent.com/44756128/117158685-43855800-ad85-11eb-94ce-c8ce513cc76b.png)

The list of resources should include your EC2 instance, which was configured and created by the main Terraform code, and 3 resources with module.vpc in the name, which were configured and created via the module code.

Tear down the infrastructure you just created before moving on:
```sh
terraform destroy
```

When prompted, type yes and press Enter.

![image](https://user-images.githubusercontent.com/44756128/117158922-7596ba00-ad85-11eb-838c-2b026f7c9e09.png)
