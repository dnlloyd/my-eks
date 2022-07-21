# Argo CD

## Install

Install

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Access via LB

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```


Get default admin pass

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

login and update password

```
kubectl get svc -n argocd

argocd login xxx.us-east-1.elb.amazonaws.com

argocd account update-password
```

## Deploy application

```
kubectl create namespace www
```

create secret for ECR

```
kubectl create secret docker-registry regcred \
  --docker-server=xxx.dkr.ecr.us-east-1.amazonaws.com \
  --docker-username=AWS \
  --docker-password=$(aws ecr get-login-password) \
  --namespace=www
```

Create web app

```
argocd app create www \
--repo https://github.com/dnlloyd/my-eks.git \
--path apps/argo-cd/www \
--dest-server https://kubernetes.default.svc \
--dest-namespace www
```