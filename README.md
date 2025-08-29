# DEPLOYING SIMPLE NGINX WEB APP ON EKS (Kubernetes)

## Installing kubectl, eksctl and k8s cluster:
-	Create EC2 instance, create IAM role and add IAM role:
-	Connect EC2 instance and enter below commands to install **kubectl**

```   
curl -LO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```
-	curl → A command-line tool for downloading files from the internet.
-	-L → Follows redirects (if the URL moves, it still downloads the file).
-	-O → Saves the file with the same name as in the URL (kubectl in this case).
-	dl.k8s.io → This is the domain name, which is Kubernetes' official download server.
-	release/ → This indicates that the file is coming from the releases section of Kubernetes.
-	linux/ → Specifies the operating system (Linux).
-	amd64/ → Specifies the architecture (64-bit AMD/Intel processors).

**Commands to install eksctl:**
```   
curl -sSLO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz"
tar -xzf eksctl_Linux_amd64.tar.gz
sudo mv eksctl /usr/local/bin/
eksctl version
```

-	curl connects to GitHub's servers to access the file.
-	GitHub redirects the request to the actual location of the latest release file.
-	It downloads the file eksctl_Linux_amd64.tar.gz.
-	The file eksctl_Linux_amd64.tar.gz (a compressed archive) starts downloading automatically.
-	The file is saved in the current directory with the same name.

 

**Commands to install k8s cluster:**
```
eksctl create cluster --name my-cluster --region us-east-1 --nodegroup-name my-nodes --nodes 2
```

-	Creates an Amazon EKS cluster named my-cluster.
-	Sets up a node group called my-nodes.
-	Deploys 2 worker nodes (EC2 instances) to run Kubernetes workloads.


## DEPLOYING SIMPLE NGINX WEB APP ON EKS :
- List nodes, create file1 nginx-deployment.yaml and add dependencies.
```
kubectl get nodes
touch nginx-deployment.yaml
vi nginx-deployment.yaml
```
```
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
```
        
```        
kubectl apply -f nginx-deployment.yaml	
kubectl get pods
touch nginx-service.yaml
vi nginx-service.yaml
```
- Kubernetes will create the Deployment and 2 Pods with NGINX
- Create file2 nginx-service.yaml add dependencies.
```
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
```
    
```
kubectl apply -f nginx-service.yaml		: 	
```
-	With above command Kubernetes will create a LoadBalancer and attach it to your pods

```  
kubectl get svc nginx-service
```

-	Kubernetes fetches details of the nginx-service from the cluster.
-	It displays the Service type, cluster IP, external IP (if applicable), and exposed ports.

Access the Application:

-	Open a browser and visit:
-	http://<EXTERNAL-IP
-	You can see the *Nginx welcome page*. It means successfully deployed the project.

- Command to delete the running cluster.

```  
eksctl delete cluster --name my-cluster --region us-east-1
```


