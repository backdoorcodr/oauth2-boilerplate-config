### Auth2 Proxy

### Step 1:

##### install nginx ingress

```shell
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress ingress-nginx/ingress-nginx --namespace default --wait 
```

#### install cert-manager if missing

```shell
helm repo add jetstack https://charts.jetstack.io
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.10.1 \
  --set installCRDs=true
```
### Use own domain

change all occurrences of `.msarimz.eu` with your own `mydomain.com` in `oauth2-proxy-full.yaml` and `demo-app.yaml`

### Update DNS
```shell
kubectl get svc -n ingress-nginx
```
Get the public IP `EXTERNAL-IP`, you might wait a bit for the public IP to be avaialble, it will be something like this

```shell
➜  chart git:(terraform) k use ingress
Switched to namespace "ingress".
➜  chart git:(terraform) k get svc
NAME                                         TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)                      AGE
ingress-ingress-nginx-controller             LoadBalancer   100.70.124.187   45.129.45.19   80:32177/TCP,443:32332/TCP   2d21h
```

update your domain with respective IPs for 

http://helloworld.mydomain.com

http://httpbin.mydomain.com

http://nodejs.mydomain.com

http://proxy.mydomain.com

### Step 2

```shell
kubectl apply -f cert-config/cert_issuer.yaml
```

### Step 3
##### Deploy secret
```shell
kubectl apply -f onedirection-secret.yml
```

### Step 4
##### Deploy stack

```shell
 kubectl apply -f oauth2-proxy-full.yaml
```

Then deploy the nodejs demo application

```shell
kubectl apply -f demo-app.yaml
```


### Ingress for testing application with auth

http://demo.msarimz.eu 
