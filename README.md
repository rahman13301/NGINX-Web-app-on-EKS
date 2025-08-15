# DEPLOYING SIMPLE NGINX WEB APP ON EKS
Installing kubectl, eksctl and k8s cluster:
-	Create EC2 instance, create IAM role and add IAM role:
-	Connect EC2 instance and enter below command to install kubectl

1.	Command to install kubectl:
   
curl -LO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl

sudo mv kubectl /usr/local/bin/

kubectl version --client

-	curl → A command-line tool for downloading files from the internet.
-	-L → Follows redirects (if the URL moves, it still downloads the file).
-	-O → Saves the file with the same name as in the URL (kubectl in this case).
-	dl.k8s.io → This is the domain name, which is Kubernetes' official download server.
-	release/ → This indicates that the file is coming from the releases section of Kubernetes.
-	linux/ → Specifies the operating system (Linux).
-	amd64/ → Specifies the architecture (64-bit AMD/Intel processors).


3.	Commands to install eksctl:
   
curl -sSLO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz"

tar -xzf eksctl_Linux_amd64.tar.gz

sudo mv eksctl /usr/local/bin/

eksctl version

-	curl connects to GitHub's servers to access the file.
-	GitHub redirects the request to the actual location of the latest release file.
-	It downloads the file eksctl_Linux_amd64.tar.gz.
-	The file eksctl_Linux_amd64.tar.gz (a compressed archive) starts downloading automatically.
-	The file is saved in the current directory with the same name.

 

3.	Commands to install k8s cluster:

eksctl create cluster --name my-cluster --region us-east-1 --nodegroup-name my-nodes --nodes 2

-	Creates an Amazon EKS cluster named my-cluster in the us-east-1 region.
-	Sets up a node group called my-nodes.
-	Deploys 2 worker nodes (EC2 instances) to run Kubernetes workloads.


DEPLOYING SIMPLE NGINX WEB APP ON EKS :

kubectl get nodes				: 	List all nodes in the cluster

touch nginx-deployment.yaml   		: 	Create file1

vi nginx-deployment.yaml 			:	Add below code into file1


apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        
        
kubectl apply -f nginx-deployment.yaml	: 	Kubernetes will create the Deployment and 2 Pods with NGINX	

kubectl get pods				:	List all running Pods

touch nginx-service.yaml			: 	Create file2

vi nginx-service.yaml 				:	Add below code into file2


apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    

kubectl apply -f nginx-service.yaml		: 	

-	Kubernetes will create a LoadBalancer and attach it to your pods
  
kubectl get svc nginx-service			:

-	Kubernetes fetches details of the nginx-service from the cluster.
-	It displays the Service type, cluster IP, external IP (if applicable), and exposed ports.

Access the Application:

-	Open a browser and visit:
-	http://<EXTERNAL-IP
-	You can see the *Nginx welcome page*.


eksctl delete cluster --name my-cluster --region us-east-1  : Delete the running clusters



