# argocd-folder
1. Configure AWS CLI and kubectl
Ensure that the AWS CLI is configured with appropriate credentials and that kubectl is set up to interact with your EKS cluster:

aws eks --region <your-region> update-kubeconfig --name <your-cluster-name>
Replace <your-region> and <your-cluster-name> with your AWS region and EKS cluster name.

2. Install ArgoCD
Create the argocd namespace and install ArgoCD:
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
This will deploy ArgoCD components into the argocd namespace.

3. Expose ArgoCD Server
To access the ArgoCD UI, expose the argocd-server service using a LoadBalancer:
Copy
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
Wait for the external IP to be provisioned. You can check the status using:
kubectl get svc argocd-server -n argocd
Alternatively, for local access within Codespaces, you can use port forwarding:
kubectl port-forward svc/argocd-server -n argocd 8080:443
4. Retrieve ArgoCD Admin Password
Obtain the initial admin password:
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
5. Access ArgoCD UI
Navigate to the ArgoCD UI using the external IP or the forwarded port:

LoadBalancer: https://<external-ip>

Port Forwarding: https://localhost:8080

Log in with the username admin and the password retrieved in the previous step.
medium.com

6. Deploy the ArgoCD Application
Apply the ArgoCD Application manifest to deploy the NGINX application:
kubectl apply -f argocd/nginx-app.yaml
ArgoCD will synchronize the application defined in your GitHub repository to the EKS cluster.

✅ Verification
Check the status of the deployed application:
kubectl get deployments
kubectl get services
Access the NGINX application using the NodePort service:
http://<node-external-ip>:30080
Replace <node-external-ip> with the external IP address of your EKS worker node.

🧹 Cleanup
kubectl delete -f argocd/nginx-app.yaml
kubectl delete -f manifests/nginx-deployment.yaml
kubectl delete -f manifests/nginx-service.yaml
kubectl delete namespace argocd

