helm repo add metallb https://metallb.github.io/metallb
helm install \
  metallb metallb/metallb \
  --namespace networking \
  --create-namespace
kubectl apply -n networking -f config.yaml
kubectl apply -n networking -f l2.yaml