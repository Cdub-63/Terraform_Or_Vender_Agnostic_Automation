![image](https://user-images.githubusercontent.com/44756128/117722505-c56eea00-b1a6-11eb-8b4d-128812f2502a.png)

Introduction
We'll be working with the Terraform fmt, taint and import commands to help understand when you may need to use them in your everyday work with Terraform.

# Clone Terraform Code and Switch to the Proper Directory
Clone the required code from the provided repository:
```sh
git clone https://github.com/linuxacademy/content-hashicorp-certified-terraform-associate-foundations.git
```

Switch to the directory where the code is located:
```sh
cd content-hashicorp-certified-terraform-associate-foundations/section4-hol1
```

List the files in the directory:
```sh
ls
```

![image](https://user-images.githubusercontent.com/44756128/117724649-aa51a980-b1a9-11eb-9506-04f43f0b59a6.png)

The files in the directory should include main.tf, README.md, and setup.tf. The main.tf file contains the code used to spin up an AWS EC2 instance (virtual machine) and the setup.tf contains the code for the resources that support the creation of the VM.

# Use the fmt Command to Format Any Unformatted Code Before Deployment
View the contents of the main.tf file using the cat command:
```sh
cat main.tf
```

![image](https://user-images.githubusercontent.com/44756128/117724722-cbb29580-b1a9-11eb-8848-9957b563ecea.png)

Notice that the code in the file is pretty messy and improperly formatted, with issues like inconstent indentation, which is making it hard to read.

Use the terraform fmt command to format the code in any file in the directory in which Terraform finds formatting issues:
```sh
terraform fmt
```

![image](https://user-images.githubusercontent.com/44756128/117724810-eab12780-b1a9-11eb-828f-90d0e3f2bef3.png)

Once the command has completed, note that Terraform returns the output main.tf, which means that Terraform found formatting issues in that file and has gone ahead and fixed those formatting issues for you.

View the contents of the the main.tf file again:
```sh
cat main.tf
```

![image](https://user-images.githubusercontent.com/44756128/117724850-f7358000-b1a9-11eb-917a-c8b096169681.png)

Notice that the code has now been formatted cleanly and consistently.

Initialize the Terraform working directory and fetch any required providers:
```sh
terraform init
```

![image](https://user-images.githubusercontent.com/44756128/117724919-0ae0e680-b1aa-11eb-9a30-6f22ee62db79.png)

Deploy the code:
```sh
terraform apply
```

![image](https://user-images.githubusercontent.com/44756128/117725043-36fc6780-b1aa-11eb-88c8-888c2a1db5f7.png)

When prompted, type yes and press Enter.

When complete, it will output the public IP for the EC2 instance that is hosting the webserver as the Webserver-Public-IP value.

# Use the taint Command to Replace a Resource
Modify the Provisioner Code for the aws_instance.webserver Resource
Using Vim, open the main.tf file:
```sh
vim main.tf
```

Note the name of the resource that is created by this code; in this case, it would be aws_instance.webserver as configured.

Inside the provisioner block, find the following line of code that outputs the content on a webpage, which currently displays Version 1:
```sh
echo '<h1><center>My Website via Terraform Version 1</center></h1>'
```

![image](https://user-images.githubusercontent.com/44756128/117725159-5e533480-b1aa-11eb-8661-ccf2e59380f0.png)

In this line of code, change Version 1 to Version 2.

Press Escape and enter :wq to save and exit the file.

# Taint the Existing aws_instance.webserver Resource
Use the terraform taint command and the name of the resource to tell Terraform to replace that resource and run the provisioner again upon the next deployment:
```sh
terraform taint aws_instance.webserver
```

View the Terraform state file to verify that the resource has been tainted:
```sh
vim terraform.tfstate
```

![image](https://user-images.githubusercontent.com/44756128/117725276-893d8880-b1aa-11eb-84fb-1f3b96a63991.png)

Search for the keyword /taint and notice that the aws_instance resource with the name webserver has a status of tainted.

Press Escape and enter :q! to exit the file.

# Deploy the Code to Rerun the Provisioner and Replace the aws_instance.webserver Resource
Deploy the code:
```sh
terraform apply
```

![image](https://user-images.githubusercontent.com/44756128/117725529-dfaac700-b1aa-11eb-80c3-e9324cb88f9a.png)

In the plan that displays before deployment, note that it will add 1 resource and destroy 1 resource, which is in essence the replacement of the old aws_instance.webserver with the new aws_instance.webserver that is configured with the modified code. Note also that it outputs a change to the public IP of the resource via the Webserver-Public-IP value.

Type yes and press Enter to deploy the code as planned.

When complete, it will output the new public IP for the webserver as the Webserver-Public-IP value.

Use the curl command to view the contents of the webpage using the IP address provided:
```sh
curl http://<WEBSERVER-PUBLIC-IP>
```

![image](https://user-images.githubusercontent.com/44756128/117725582-f3eec400-b1aa-11eb-85e3-30d90765f6d5.png)

In the output that is returned, verify that it returns My Website via Terraform Version 2, validating that the provisioner was successfully run again and the tainted resource (which contained code for Version 1) was replaced with a new resource (which contained code for Version 2).

Note: Alternately, you could open the IP address in a web browser to view the webpage's contents.

Use the import Command to Import a Resource

# Add the VM as a Resource Named aws_instance.webserver2 in Your Code
View the contents of the resource_ids.txt file:
```sh
cat /home/cloud_user/resource_ids.txt
```

Copy the EC2 instance ID that is displayed in the contents of the file.

Open the main.tf file to modify it:
```sh
vim main.tf
```

At the bottom of the code, insert a new line and add the associated resource that will be named aws_instance.webserver2 into your main Terraform code:
```sh
resource "aws_instance" "webserver2" {}
```

Press Escape and enter :wq to save and exit the file.

# Import the aws_instance.webserver2 Resource to Your Terraform Configuration
Use the terraform import command, the name of the resource in your main code, and the EC2 instance ID to tell Terraform which resource to import:
```sh
terraform import aws_instance.webserver2 <COPIED-EC2-INSTANCE-ID>
```

![image](https://user-images.githubusercontent.com/44756128/117726110-bb9bb580-b1ab-11eb-8e97-5b95db82162a.png)

View the Terraform state file to verify that the resource has been imported:
```sh
vim terraform.tfstate
```

![image](https://user-images.githubusercontent.com/44756128/117726176-d1a97600-b1ab-11eb-9dd7-5a33144a7ac3.png)

Search for the keyword /webserver2 and notice that the aws_instance resource with the name webserver2 is listed and has a mode of managed.

Press Escape and enter :q! to exit the file.

# Modify the aws_instance.webserver2 Resource
Open the main.tf file to modify it:
```sh
 vim main.tf
 ```
 
At the bottom of the file, replace the existing code for the aws_instance.webserver2 resource with the following:
```tf
resource "aws_instance" "webserver2" {
   ami = data.aws_ssm_parameter.webserver-ami.value
   instance_type = "t3.micro"
   subnet_id = aws_subnet.subnet.id
 }
 ```
 
 ![image](https://user-images.githubusercontent.com/44756128/117726454-3795fd80-b1ac-11eb-8db9-f81bc305e42f.png)

Note: Alternately, you could copy/paste the ami, instance_type, and subnet_id values from the aws_instance.webserver resource in the main.tf file and add it to the existing aws_instance.webserver2 resource in the code.

As a best practice, format the code before deployment:
```sh
terraform fmt
```

Deploy the updated code:
```sh
terraform apply
```

![image](https://user-images.githubusercontent.com/44756128/117726619-6dd37d00-b1ac-11eb-8d05-12fc97734ede.png)

In the plan that displays before deployment, note that it will add 1 resource and destroy 1 resource, which is in essence the replacement of the old aws_instance.webserver2 with the new aws_instance.webserver2 that is configured with the modified code. You can also scroll up in the plan and verify that aws_instance.webserver2 will be replaced.

Type yes and press Enter to deploy the code as planned.

In the output that is returned, verify that 1 resource was added and 1 was destroyed, validating that the old aws_instance.webserver2 resource was replaced with the new aws_instance.webserver2 containing your customized code.

Tear down the infrastructure you just created before moving on:
```sh
terraform destroy
```

![image](https://user-images.githubusercontent.com/44756128/117726733-9fe4df00-b1ac-11eb-878b-b8a09042d4c7.png)

When prompted, type yes and press Enter.
