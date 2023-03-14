## Kube
<details>
<summary>Install kubectl</summary>

 ### Ref https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/
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