# k8s deploy note (digital ocean)

> [!WARNING]
> I just learned Kubernetes in one night. My notes are just for myself.

## push image to registry
```bash
docker tag hello registry.digitalocean.com/hello/hello
docker push registry.digitalocean.com/hello/hello
```

## connect cluster
```bash
doctl kubernetes cluster kubeconfig save {your-cluster}
```

#### check cluster
```bash
kubectl config get-contexts
```

## create deployment
```bash
kubectl apply -f k8s/deployment.yaml
```

#### check pod
```bash
kubectl get pod
```

## create service

In this case, use cluster ip
```bash
kubectl apply -f k8s/service.yaml
```

#### check srvice
```bash
kubectl get services
```

## install ingress

In this case, `k8s/nginx-values.yaml` to expose custom port at load-balancer
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx -f k8s/nginx-values.yaml
kubectl apply -f k8s/ingress.yaml
```

#### check ingress
```bash
kubectl get service --namespace default ingress-nginx-controller --output wide
```

## install cert-manager
```bash
kubectl create namespace cert-manager
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.10.1 --set installCRDs=true
kubectl apply -f k8s/issuer.yaml
```

#### check cert
```bash
kubectl describe certificate {your-cert}
```

## auto scale
follow https://docs.digitalocean.com/products/kubernetes/how-to/set-up-autoscaling

```bash
kubectl apply -f k8s/hpa.yaml
```

#### check auto scale
```bash
kubectl top nodes
kubectl get hpa
```

## Others

update nginx value
```bash
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx -f k8s/nginx-values.yaml
```

re-deploy
```bash
kubectl rollout restart deployment
```

## Reference

https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-on-digitalocean-kubernetes-using-helm

https://serverfault.com/questions/1094259/how-do-i-create-a-kubernetes-service-that-routes-traffic-to-different-pods-based
