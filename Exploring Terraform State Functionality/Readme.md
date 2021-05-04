![image](https://user-images.githubusercontent.com/44756128/117014109-161da900-acb6-11eb-9b76-07c8cb3f0bb0.png)

# Introduction
Understanding and being comfortable with Terraform state is crucial to mastering it. We will deploy resources using Kubernetes, a container orchestration tool, and see how Terraform maintains a state file to track changes and deploy containers declaratively.

# Check Terraform and Minikube Status
Check the Terraform status using the version command:
```sh
terraform version
```

![image](https://user-images.githubusercontent.com/44756128/117015141-10749300-acb7-11eb-96c8-b3cead8a2114.png)

Since the Terraform version is returned, you have validated that Terraform is installed and functioning properly.

Check the minikube status:
```sh
minikube status
```

The minikube status command should return host,kubelet, and apiserver in Running state and kubeconfig in Configured state. It will look something similar to this:
```
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

![image](https://user-images.githubusercontent.com/44756128/117015262-26825380-acb7-11eb-800d-da7f6ed41495.png)

# Clone Terraform Code
The Terraform code required for this lab has already been cloned onto the provided VM. Switch to the directory where the code is located:
```sh
cd lab_code/
cd section2-hol1/
```

View the code in the main.tf file:
```sh
vim main.tf
```

![image](https://user-images.githubusercontent.com/44756128/117015473-65180e00-acb7-11eb-9bb3-74909256c890.png)

The code is configured with Kubernetes as the provider, allowing Terraform to interact with the Kubernetes API to create and destroy resources. Within the kubernetes_deployment resource, the replicas attribute controls the number of deployments, which in turn controls the number of pods being deployed.

Press Escape and enter :q! to exit the file.

# Deploy Terraform Code And Observe the State File
## Deploy the Cloned Terraform Code
Initialize the working directory and download the required providers:
```sh
terraform init
```

![image](https://user-images.githubusercontent.com/44756128/117015636-8a0c8100-acb7-11eb-9768-d38485eab66c.png)

Review the actions that will be performed when you deploy the Terraform code:
```sh
terraform plan
```

![image](https://user-images.githubusercontent.com/44756128/117015739-9f81ab00-acb7-11eb-85a3-78c72b5f581f.png)

![image](https://user-images.githubusercontent.com/44756128/117015775-a9a3a980-acb7-11eb-9694-d14f7da46f3d.png)

In this case, it will create 2 resources as configured in the Terraform code.

List the files in the directory:
```sh
ls
```

Notice that the list of files does not include the terraform.tfstate at this time. You must deploy the Terraform code for the state file to be created.

Deploy the code:
```sh
terraform apply
```

![image](https://user-images.githubusercontent.com/44756128/117015973-db1c7500-acb7-11eb-99f6-b1c1562aa5a8.png)

When prompted, type yes and press Enter.

![image](https://user-images.githubusercontent.com/44756128/117016041-ea9bbe00-acb7-11eb-826f-ad19c88f36a9.png)

## Observe How the Terraform State File Tracks Resources
Once the code has executed successfully, list the files in the directory:
```sh
ls
```

![image](https://user-images.githubusercontent.com/44756128/117016071-f6878000-acb7-11eb-92e1-707a50c2a2e9.png)

Notice that the terraform.tfstate file is now listed. This state file tracks all the resources that Terraform has created.

Optionally, verify that the pods required were created by the code as configured using kubectl:
```sh
kubectl get pods
```

![image](https://user-images.githubusercontent.com/44756128/117016134-07d08c80-acb8-11eb-89fa-b3116b12dfd3.png)

There are currently 2 pods in the deployment.

List all the resources being tracked by the Terraform state file using the terraform state command:
```sh
terraform state list
```

![image](https://user-images.githubusercontent.com/44756128/117016196-14ed7b80-acb8-11eb-8aed-ad48f13b786c.png)

There are two resources being tracked: kubernetes_deployment.tf-k8s-deployment and kubernetes_service.tf-k8s-service.

View the replicas attribute being tracked by the Terraform state file using grep and the kubernetes_deployment.tf-k8s-deployment resource:
```sh
terraform state show kubernetes_deployment.tf-k8s-deployment | egrep replicas
```

![image](https://user-images.githubusercontent.com/44756128/117016285-2b93d280-acb8-11eb-8cb5-a528007eff33.png)

There should be 2 replicas being tracked by the state file.

Open the main.tf file to edit it:
```sh
vim main.tf
```

![image](https://user-images.githubusercontent.com/44756128/117016414-4d8d5500-acb8-11eb-8104-619c19ab282f.png)

Change the integer value for the replicas attribute from 2 to 4.

Press Escape and enter :wq to save and exit the file.

Review the actions that will be performed when you deploy the Terraform code:
```sh
terraform plan
```

![image](https://user-images.githubusercontent.com/44756128/117016547-6eee4100-acb8-11eb-8813-0b06f5d36e42.png)

![image](https://user-images.githubusercontent.com/44756128/117016478-5f6ef800-acb8-11eb-96bc-54a7555c171d.png)

In this case, 1 resource will change: the kubernetes_deployment.tf-k8s-deployment resource for which we have updated the replicas attribute in our Terraform code.

Deploy the code again:
```sh
terraform apply
```

![image](https://user-images.githubusercontent.com/44756128/117016653-89281f00-acb8-11eb-8e05-18d10c2eb49a.png)

When prompted, type yes and press Enter.

Optionally, verify that the pods required were created by the code as configured:
```sh
kubectl get pods
```

![image](https://user-images.githubusercontent.com/44756128/117016708-96450e00-acb8-11eb-9c0b-ea17496d0b70.png)

There are now 4 pods in the deployment.

View the replicas attribute being tracked by the Terraform state file again:
```sh
terraform state show kubernetes_deployment.tf-k8s-deployment | egrep replicas
```

![image](https://user-images.githubusercontent.com/44756128/117016760-a2c96680-acb8-11eb-9662-63f508430198.png)

There should now be 4 replicas being tracked by the Terraform state file. It is accurately tracking all changes being made to the Terraform code.

## Tear Down the Infrastructure
Remove the infrastructure you just created:
```sh
terraform destroy
```

![image](https://user-images.githubusercontent.com/44756128/117016853-b8d72700-acb8-11eb-8567-175bca918832.png)

When prompted, type yes and press Enter.

List the files in the directory:
```sh
ls
```

![image](https://user-images.githubusercontent.com/44756128/117016893-c55b7f80-acb8-11eb-8f78-40bc2ee22665.png)

Notice that Terraform leaves behind a backup file — terraform.tfstate.backup — in case you need to recover to the last deployed Terraform state.
