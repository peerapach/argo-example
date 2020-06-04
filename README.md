# EXAMPLE

## ArgoCD
### Install ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Create example applications 
```
kubectl apply -f argocd/flask-example.yaml
```

## Argo Workflow

### Install Argo
```
$ kubectl create namespace argo
$ kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/stable/manifests/install.yaml
```

### Install Minio
```
$ brew install helm # mac, helm 3.x
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
$ helm install argo-artifacts stable/minio --set service.type=NodePort --set fullnameOverride=argo-artifacts
```

### Update configmap by adding below to workflow-controller-configmap
```
$ kubectl edit cm -n argo workflow-controller-configmap
```
```
...
data:
  config: |
    artifactRepository:
      s3:
        bucket: my-bucket
        endpoint: argo-artifacts.default:9000
        insecure: true
        accessKeySecret:
          name: argo-artifacts
          key: accesskey
        secretKeySecret:
          name: argo-artifacts
          key: secretkey
```

### Create secret for docker registry login
```
$ kubectl create secret docker-registry regcred --docker-username=USERNAME --docker-password=PASSWORD -n argo          
```
### Test Argo-Workflow with artifact
```
$ argo submit --watch https://raw.githubusercontent.com/peerapach/gitops-argo/master/argo-workflow-artifact-pass.yaml
```
