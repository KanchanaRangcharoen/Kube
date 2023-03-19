# Kubernetes

## Step on Work
**_Ref :_** https://github.com/DNujira/Kube#readme
<details>
<summary>Install kubectl</summary>

### Ref <https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/>

- Download kubectl , use this command

    ```
    curl.exe -LO "https://dl.k8s.io/release/v1.26.0/bin/windows/amd64/kubectl.exe"
    ```

- Add Path
  - Open environment
  - Click Environment Variables
  - Select Path Click Edit
  - Click New
  - Add path kubectl.exe
  
    ![Screenshot 2023-03-14 162035](https://user-images.githubusercontent.com/119097660/224956110-acbca127-787d-4ef8-88fd-05818c00a0c6.png)
  - Click Ok

- Test Kubectl activation by looking at version details

    ```
    kubectl version --client --output=yaml
    ```

</details>

<details>
<summary>Install minikube</summary>

### Ref <https://minikube.sigs.k8s.io/docs/start/>

- Download minikube via PowerShell

    ```
    New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
    Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
    ```

- Add Path
  - Open environment
  - Click Environment Variables
  - Select Path Click Edit
  - Click New
  - Add path minikube.exe
  
    ![Screenshot 2023-03-14 162035](https://user-images.githubusercontent.com/119097660/224956110-acbca127-787d-4ef8-88fd-05818c00a0c6.png)
  - Click Ok

</details>

<details>
<summary>Install docker desktop</summary>

### Ref <https://docs.docker.com/desktop/install/windows-install/>
### Ref <https://minikube.sigs.k8s.io/docs/drivers/docker/>

- Download Docker Desktop for Windows
![Screenshot 2023-03-19 135401](https://user-images.githubusercontent.com/119097660/226159166-fa69742d-3774-47df-a6b1-e09498196f2c.png)

- Start a cluster using the docker driver
   ```
  minikube start --driver=docker
   ```
  ![Screenshot 2023-03-19 135752](https://user-images.githubusercontent.com/119097660/226159356-cd398051-9e50-4435-8f53-843353f96748.png)
</details>

## Deploy traefik

- Set Domain in file host in path windows

  ```
  C:\Windows\System32\drivers\etc\hosts # ex. EXTERNAL-IP traefik.spcn08.local
  ```

  ![Screenshot 2023-03-19 132447](https://user-images.githubusercontent.com/119097660/226159589-0e8ba72c-fe2d-4b75-bd2c-5dc5491a48a2.png)

- Install Traefik 
  
  ```
  kubectl apply -f https://raw.githubusercontent.com/traefik/traefik/v2.9/docs/content/reference/dynamic-configuration/kubernetes-crd-definition-v1.yml
  ```

- Install RBAC for Traefik
  
  ```
  kubectl apply -f https://raw.githubusercontent.com/traefik/traefik/v2.9/docs/content/reference/dynamic-configuration/kubernetes-crd-rbac.yml
  ```

- create namespace
  
  ```
  kubectl create namespace <name>
  # ex. kubectl create namespace spcn08
  ```

- Install Traefik Helmchart
  
  ```
  helm repo add traefik https://traefik.github.io/charts
  helm repo update
  helm install traefik traefik/traefik
  ```

  >**_WARNING :_** To run the command you need to install **[helm](https://get.helm.sh/helm-v3.11.2-windows-amd64.zip)** and add the helm.exe binary to your path

- Check if the service runs

  ```
  kubectl get svc -l app.kubernetes.io/name=traefik
  kubectl get po -l app.kubernetes.io/name=traefik
  ```
  ![Screenshot 2023-03-19 141816](https://user-images.githubusercontent.com/119097660/226160134-a7427c66-ec83-4749-9c8e-f022838989b7.png)

- Create a tunnel to use as EXTERNAL-IP

  ```
  minikube tunnel
  ```

- Create secrete
  
  ```
  htpasswd -nB <nameuser> | tee auth-secret 
  # ex. htpasswd -nB spcn08 | tee auth-secret
  ```
  ![Screenshot 2023-03-19 133237](https://user-images.githubusercontent.com/119097660/226160304-fc2d5eb5-4a4c-4372-8999-b6e4951e8c85.png)
  >**_WARNING :_** I run it on WSL

- Dry run to create a secret deployment

  ```
  kubectl create secret generic -n traefik dashboard-auth-secret --from-file=users=auth-secret -o yaml --dry-run=client | tee dashboard-secret.yaml
  ```

  ![Screenshot 2023-03-19 133217](https://user-images.githubusercontent.com/119097660/226160440-1a365831-8666-4adb-979b-40107abbfd7a.png)
  When the run is complete, the file dashboard-secret.yaml and bring user data to put in the file traefik-dashboard.yaml
  ![Screenshot 2023-03-19 133246](https://user-images.githubusercontent.com/119097660/226160538-61566061-6b7f-4fbf-bbdb-ed50cff102ab.png)

  ![Screenshot 2023-03-19 143040](https://user-images.githubusercontent.com/119097660/226160542-d56bd38b-78e9-4884-a697-49ffb7ded1be.png)

- Deploy

  ```
  kubectl apply -f traefik-dashboard.yaml  
  ```
  >**_WARNING :_** you must run minikube dashboard for get cluster dashboard and minikube tunnel for route to services 
  
  **Link on local :** https://traefik.spcn08.local/dashboard/#/
  ![Screenshot 2023-03-19 133906](https://user-images.githubusercontent.com/119097660/226160796-46fb4998-f137-482a-8c7a-ebc6416755f0.png)

## Deploy rancher/hello-world

- Set Domain in file host in path windows

  ```
  C:\Windows\System32\drivers\etc\hosts # ex. EXTERNAL-IP web.spcn08.local
  ```
  ![Screenshot 2023-03-19 132447](https://user-images.githubusercontent.com/119097660/226159589-0e8ba72c-fe2d-4b75-bd2c-5dc5491a48a2.png)

- Create rancher-hello-world.yaml 
  
  <details>
  <summary>SHOW CODE rancher-hello-world.yaml</summary>

  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: rancher-deployment
    namespace: spcn08
  spec:
    replicas: 1
    selector:
      matchLabels:
        app: rancher
    template:
      metadata:
        labels:
          app: rancher
      spec:
        containers:
        - name: rancher
          image: rancher/hello-world
          ports:
          - containerPort: 80
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: rancher-service
    labels:
      name: rancher-service
    namespace: spcn08
  spec:
    selector:
      app: rancher
    ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 80
  ```
  </details>

- Create service.yaml 
  
  <details>
  <summary>SHOW CODE service.yaml</summary>

  ```
  apiVersion: traefik.containo.us/v1alpha1
  kind: IngressRoute
  metadata:
    name: service-ingress
    namespace: spcn08
  spec:
    entryPoints:
      - web
      - websecure
    routes:
    - match: Host(`web.spcn08.local`)
      kind: Rule
      services:
      - name: rancher-service
        port: 80
  ```
  </details>

- Deploy

  ```
  kubectl apply -f rancher-hello-world.yaml
  kubectl apply -f service.yaml
  ```
  >**_WARNING :_** you must run minikube dashboard for get cluster dashboard and minikube tunnel for route to services 
  
  **Link on local :** http://web.spcn08.local/
  ![Screenshot 2023-03-19 134331](https://user-images.githubusercontent.com/119097660/226162275-4b6ecebc-f70d-4b7d-a673-27e61c010557.png)