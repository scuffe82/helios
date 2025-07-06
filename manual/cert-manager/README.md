helm repo add jetstack https://charts.jetstack.io --force-update
helm install \
  cert-manager jetstack/cert-manager \
  --namespace security \
  --create-namespace \
  --values values.yaml
# create sealed secret
kubectl apply -n cert-manager -f sealed-secret.yaml
kubectl apply -n cert-manager -f issuers.yaml

