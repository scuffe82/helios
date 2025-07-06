helm repo add traefik https://traefik.github.io/charts
helm repo update
kubectl apply -f spenceops-com-wildcard.yaml
## wait for cert to create
helm install \
  traefik-internal traefik/traefik \
  --namespace networking \
  --values values.yaml
kubectl apply -f tls-store.yaml