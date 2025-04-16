
## 1. Kubernetes-cluster configure

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

nodes:
- role: control-plane
  image: kindest/node:v1.30.0
- role: worker
  image: kindest/node:v1.30.0
- role: worker
  image: kindest/node:v1.30.0


## 2. Installing Argo CD

Before that we have install kubectl and Kind

- Create a namespace for Argo CD:

    sudo kubectl create namespace argocd

- Apply the Argo CD manifest:

    sudo kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

- Check the services in Argo CD namespace:

    sudo kubectl get svc -n argocd

- Expose Argo CD server using NodePort(Change the type of argocd-server ClusterIP to NodePort):

    sudo kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'

    sudo kubectl get svc -n argocd

- Forward ports to access Argo CD server:

    sudo kubectl port-forward -n argocd service/argocd-server 9090:443 --address 0.0.0.0

- Check with IP address like Jenkins


## 3. Argo CD Initial Admin Password

    Default username: admin
    Password: sudo kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo


## 4.Install HELM

sudo curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

ls

sudo chmod 700 get_helm.sh

sudo ./get_helm.sh

## 5. Install Kube Prometheus Stack

sudo helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

sudo helm repo add stable https://charts.helm.sh/stable

sudo helm repo update

sudo kubectl create namespace monitoring

sudo helm install kind-prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --set prometheus.service.nodePort=30000 --set prometheus.service.type=NodePort --set grafana.service.nodePort=31000 --set grafana.service.type=NodePort --set prometheus-node-exporter.service.nodePort=32001 --set prometheus-node-exporter.service.type=NodePort

sudo kubectl get svc -n monitoring

sudo kubectl get namespace

- Port-forwarding to Prometheus and Grafana

sudo kubectl port-forward svc/kind-prometheus-kube-prome-prometheus -n monitoring 9080:9090 --address=0.0.0.0

sudo kubectl get svc -n monitoring

sudo kubectl port-forward svc/kind-prometheus-grafana -n monitoring 31000:80 --address=0.0.0.0

- Username and Password of Grafana

username of Grafana is admin
password of Grafana is prom-operator
