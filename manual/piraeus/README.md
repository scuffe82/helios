kubectl apply --server-side -f "https://github.com/piraeusdatastore/piraeus-operator/releases/latest/download/manifest.yaml"
kubectl wait pod --for=condition=Ready -n piraeus-datastore -l app.kubernetes.io/component=piraeus-operator
kubectl apply -f linstore-cluster.yaml
kubectl wait pod --for=condition=Ready -n piraeus-datastore -l app.kubernetes.io/name=piraeus-datastore
kubectl -n piraeus-datastore exec deploy/linstor-controller -- linstor node list
kubectl apply -f linstore-config.yaml
kubectl -n piraeus-datastore exec deploy/linstor-controller -- linstor node list
kubectl -n piraeus-datastore exec deploy/linstor-controller -- linstor storage-pool list
kubectl apply -f storage-class.yaml

