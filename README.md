### Auth2 Proxy

### **Step 1**:

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
### Use your own domain

change all occurrences of `.msarimz.eu` with your own `mydomain.com` in `oauth2-proxy-full.yaml` and `demo-app.yaml`

### Update DNS
Get the public IP `EXTERNAL-IP`, you might wait a bit for the public IP to be avaialble, it will be something like this
```shell
kubectl get svc --namespace=default ingress-ingress-nginx-controller -o jsonpath='{.status.loadBalancer.ingress[0].ip}'; echo
```
update the received IP in your domain

### **Step 2**

```shell
kubectl apply -f cert-config/cert_issuer.yaml
```

```shell
kubectl apply -f cert-config/demo-app-certificate.yaml
```

### **Step 3**
##### Deploy secret
```shell
kubectl apply -f demo-app-reg-secret.yml
```

### **Step 4**
##### Deploy infrastructure

### If IdP is needed, lets get the dex running: 

```shell
 kubectl apply -f idp/dex.yaml 
```

### Once dex is ready, provision the necessary infra: 

```shell
 kubectl apply -f oauth2-proxy-infra.yaml
```

### **Demo application deployment**: 

Then deploy the demo application

```shell
kubectl apply -f demo-app.yaml
```
# Try it out: 
Go to: http://demo.msarimz.eu 
Email: admin@example.com
Password: password

## To cleanup: 

### Delete infra, idp plus application: 
kubectl delete -f idp/dex.yaml -f oauth2-proxy-infra.yaml -f demo-app.yaml 

### Delete secrets, certificate and cert-manager
kubectl delete -f demo-app-reg-secret.yml
kubectl delete -f cert-config/demo-app-certificate.yaml
helm uninstall cert-manager jetstack/cert-manager --namespace cert-manager

