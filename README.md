# udacity-cloud-c03

# Installing Udagram Project 3 - Refactor Udagram app into Microservices 
The project is microservices project of `Udagram` monolith application. There are two `feed` api backends with two versions of the project as A/B tests. 

## Tasks

### Prerequisites
- **kubeone** --  Instructions at <a href='https://github.com/kubermatic/kubeone/blob/master/README.md'>README</a> and <a href='https://github.com/kubermatic/kubeone/blob/master/docs/quickstart-aws.md'>AWS</a>
- **terraform** -- Instructions at <a href='https://www.terraform.io/downloads.html'>Terraform website</a>


### Creating Infrastructure with terraform 

1. Download the project and navigate to `udacity-cloud-c03/udacity-c3-deployment/tf` folder
2. To plan terraform infrastructure : `terraform plan`
3. To pply the infrastructure settings : `terraform apply`
	- Enter `yes` to confirm 
4. Save terraform settings in json file for kubeone: `terraform output -json > tf.json`
5. Install Kubernetes using configuration output from Terraform: `kubeone install config.yaml --tfjson tf.json`
6. Setup KUEBCONFIG variable that will be used by kubectl commands `export KUBECONFIG=$PWD/project-name`

### Setup Kubernetes environment
You will need to install the kubectl command. Open a new terminal within the project directory and run:

1. Generate encrypted values for aws credentials, Database User Name, and Database Password using bcrypt and put the values into `aws-secret.yaml` and `env-secret.yaml` files and load secret files: 
	- `kubectl apply -f aws-secret.yaml && kubectl apply -f env-secret.yaml`
2. Load config map that has raw data in it : `kubectl apply -f env-configmap.yaml`
3. Apply deployments settings :
	- `kubectl apply -f backend-feed-deployment.yaml && kubectl apply -f frontend-deployment.yaml && kubectl apply -f backend-user-deployment.yaml`
5. Apply kubctl services:
	- `kubectl apply -f backend-feed-service.yaml && kubectl apply -f backend-user-service.yaml && kubectl apply -f frontend-service.yaml`
6. Deploy reverse proxy, has to be done after the services are running:
	- `kubectl apply -f reverseproxy-deployment.yaml`
	- `kubectl apply -f reverseproxy-service.yaml`
7. Perform port forwarding (each needs to be run in a separate terminal window and left running)
	- `kubectl port-forward service/frontend 8100:8100`
	- `kubectl port-forward service/reverseproxy 8080:8080`


### To check status and other information :
  - `kubectl get nodes`
  - `kubectl get pod`
  - `kubectl get svc`
  - `kubectl get configmaps`
  - `kubectl get secrets`
  - `kubectl get all`

### Continuous Integration / Continuous Development:
- This project has Travis CI implementation that monitors Github updates and deploys docker containers automatically. Learn more about Travis CI env variable at <a href='https://docs.travis-ci.com/user/docker/'>Travis CI docs</a>.
