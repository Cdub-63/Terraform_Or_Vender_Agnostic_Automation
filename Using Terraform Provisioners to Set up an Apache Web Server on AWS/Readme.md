![image](https://user-images.githubusercontent.com/44756128/116884031-e1ddb600-abeb-11eb-8c5c-d406ead1ee20.png)

# Introduction
We'll be using a Terraform provisioner to custom bootstrap a VM in AWS and install a webserver on it, and then test that the webserver is working as expected.

# Clone Terraform Code and Switch to the Proper Directory
Clone the required code from the provided repository:
```sh
git clone https://github.com/linuxacademy/content-hashicorp-certified-terraform-associate-foundations.git
```

Switch to the directory where the code is located:
```sh
cd content-hashicorp-certified-terraform-associate-foundations/section3-hol2/
```

List the files in the directory:
```sh
ls
```

![image](https://user-images.githubusercontent.com/44756128/116884908-e787cb80-abec-11eb-98ce-7e6e77f446b5.png)

The files in the directory should include main.tf, README.md, and setup.tf.

# Examine the Code in the main.tf File
View the contents of the main.tf file using the cat command:
```sh
cat main.tf
```

![image](https://user-images.githubusercontent.com/44756128/116885182-37669280-abed-11eb-8be2-56d92f272400.png)

Examine the code in the resource block and note the following:
  - We are creating an AWS EC2 instance (virtual machine) named webserver.
  - We are passing a number of parameters for the resource, such as the AMI that the VM will be spun up as, the instance type, the private key that the instance will be using, the public IP attached to the instance, the security group applied to the instance, and the subnet ID where the VM will be spun up.

Note: All of these resources are actually being created via the setup.tf file, which you can view if desired.

Examine the code in the provisioner block and note the following:
  - The remote-exec keyword tells us that this is a remote provisioner which invokes a script on a remote resource after it is created.
  - The provisioner is using the parameters configured in the embedded connection block to connect to the AWS EC2 instance being created.
  - The provisioner will then issue the commands configured in the inline block to install Apache webserver on CentOS through the yum package manager, start up the Apache server, create a single web page called My Test Website With Help From Terraform Provisioner as an index.html file, and move that file into the data directory of the webserver to be served out globally.

# Deploy the Code and Access the Bootstrapped Webserver
Initialize the Terraform working directory and download the required providers:
```sh
terraform init
```

![image](https://user-images.githubusercontent.com/44756128/116885460-86142c80-abed-11eb-9bef-0f82637bec9a.png)

Validate the code to look for any errors in syntax, parameters, or attributes within Terraform resources that may prevent it from deploying correctly:
```sh
terraform validate
```

You should receive a notification that the configuration is valid.

![image](https://user-images.githubusercontent.com/44756128/116885569-a348fb00-abed-11eb-86b7-691b86a1d111.png)

Review the actions that will be performed when you deploy the Terraform code:
```sh
terraform plan
```

![image](https://user-images.githubusercontent.com/44756128/116885606-acd26300-abed-11eb-886c-b34dbe062d39.png)

In this case, it will create 7 resources as configured in the Terraform code.

Deploy the code:
```sh
terraform apply
```

![image](https://user-images.githubusercontent.com/44756128/116885772-e1461f00-abed-11eb-994f-2d53410b00c9.png)

When prompted, type yes and press Enter.

As the code is being deployed, you will notice that the Terraform provisioner tries to connect to the EC2 instance and, once that connection is established, it will run the bootstrapping that was configured in the provisioner block against the instance.

When complete, it will output the public IP for the Apache webserver as the Webserver-Public-IP value.

![image](https://user-images.githubusercontent.com/44756128/116885825-edca7780-abed-11eb-8984-c772883bd2e2.png)

Copy the IP address, paste it in a new browser window or tab, and press Enter.

Verify that the web page displays as My Test Website With Help From Terraform Provisioner, validating that the provisioner within your code worked as intended: the commands configured in the provisioner code were issued and executed successfully on the EC2 instance that was created.

![image](https://user-images.githubusercontent.com/44756128/116885871-00dd4780-abee-11eb-9a3d-128e2cae36d9.png)

![image](https://user-images.githubusercontent.com/44756128/116885910-10f52700-abee-11eb-9104-9cb6374d08bc.png)

