# KUBERNETES:

<img width="712" height="867" alt="image" src="https://github.com/user-attachments/assets/9ff473e0-765d-4d68-8a3a-622233ae6d75" />


## Installing kubectl, eksctl and k8s cluster:
-	Create EC2 instance, create IAM role and add IAM role:

<img width="1503" height="341" alt="image" src="https://github.com/user-attachments/assets/bc90c7bf-62e4-4639-9e5b-125dc4db6389" />
<img width="1079" height="702" alt="image" src="https://github.com/user-attachments/assets/67420ef5-6c67-422b-8d0f-6febecd2b9eb" />
  
-	Connect EC2 instance and enter below commands to install **kubectl**
  <img width="1183" height="888" alt="image" src="https://github.com/user-attachments/assets/2b35ac2d-a808-4ed7-88be-d99ec68897af" />




```   
curl -LO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```
<img width="1233" height="233" alt="image" src="https://github.com/user-attachments/assets/c206d12d-4302-45bc-9cc1-a1190eb0a9d9" />

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
<img width="1197" height="138" alt="image" src="https://github.com/user-attachments/assets/9c20b64c-fee6-480f-8ebe-e8719676f49e" />

-	curl connects to GitHub's servers to access the file.
-	GitHub redirects the request to the actual location of the latest release file.
-	It downloads the file eksctl_Linux_amd64.tar.gz.
-	The file eksctl_Linux_amd64.tar.gz (a compressed archive) starts downloading automatically.
-	The file is saved in the current directory with the same name.

 

**Commands to install k8s cluster:**
- Change the attached IAM role policy permission to install k8s clusters. Try re attach the IAM role.
  
<img width="798" height="665" alt="image" src="https://github.com/user-attachments/assets/e63ab7e7-6df8-4fb8-a37f-624a6b578fe4" />

```
eksctl create cluster --name my-cluster --region us-east-1 --nodegroup-name my-nodes --nodes 2
```
- We can see clusters are creating.

<img width="1897" height="681" alt="image" src="https://github.com/user-attachments/assets/e6509858-f991-4505-8bf3-d26582a40eb8" />

<img width="1886" height="290" alt="image" src="https://github.com/user-attachments/assets/19b56918-ff28-4bfd-8e49-c1cac2f57507" />

<img width="1555" height="268" alt="image" src="https://github.com/user-attachments/assets/d9fb0190-4025-4029-8ab4-02076300cdd3" />


  
-	Created an Amazon EKS cluster named my-cluster.
-	Sets up a node group called my-nodes.
-	Deploys 2 worker nodes (EC2 instances) to run Kubernetes workloads.


## DEPLOYING SIMPLE NGINX WEB APP ON EKS :

- This is required for authenticating with EKS clusters. Install aws-iam-authenticator in Ubuntu.

  ```
# Download
curl -o aws-iam-authenticator https://s3.us-west-2.amazonaws.com/amazon-eks/1.28.3/2023-11-14/bin/linux/amd64/aws-iam-authenticator

# Make executable and move
chmod +x aws-iam-authenticator
sudo mv aws-iam-authenticator /usr/local/bin/
kubectl get nodes
```
  
  <img width="1300" height="133" alt="image" src="https://github.com/user-attachments/assets/3c22bc77-f039-450b-a9e5-70034b45933b" />
  <img width="799" height="115" alt="image" src="https://github.com/user-attachments/assets/a729695e-ceaa-43bf-bd8a-c82f804f488c" />


- List nodes, create file1 nginx-deployment.yaml and add dependencies.

```
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
- Kubernetes will create the Deployment and 2 Pods with NGINX through below commands       
```        
kubectl apply -f nginx-deployment.yaml
kubectl get pods
```
- Create file2 nginx-service.yaml add below dependencies into the file2.
```
touch nginx-service.yaml
vi nginx-service.yaml
```

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
- Kubernetes will create the Deployment and 2 Pods with NGINX through below command.    
```
kubectl apply -f nginx-service.yaml
```
-	With above command Kubernetes will create a LoadBalancer and attach it to your pods

```  
kubectl get svc nginx-service
```

-	Kubernetes fetches details of the nginx-service from the cluster.
-	It displays the Service type, cluster IP, external IP (if applicable), and exposed ports.

- **Access the Application:**

-	Open a browser and visit:
-	http://<EXTERNAL-IP
-	You can see the *Nginx welcome page*. It means successfully deployed the project.

- **Command to delete the running cluster**.

```  
eksctl delete cluster --name my-cluster --region us-east-1
```

Project successfully completed.
