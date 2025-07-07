kubectl apply -f pvc.yaml
kubectl get pvc
kubectl apply -f pod.yaml
kubectl wait pod --for=condition=Ready -l app.kubernetes.io/name=web-server
kubectl exec deploy/web-server -- df -h /usr/share/nginx/html
kubectl -n piraeus-datastore exec deploy/linstor-controller -- linstor resource list-volumes
# device name and file system should be the same

# test repilicated volume
kubectl apply -f pvc-multi.yaml
kubectl apply -f pod-multi.yaml
kubectl wait pod --for=condition=Ready -l app.kubernetes.io/name=volume-logger
kubectl exec deploy/volume-logger -- cat /volume/hello
kubectl cordon <name name where the pod is running>
kubectl rollout restart deploy/volume-logger
kubectl exec deploy/volume-logger -- cat /volume/hello
# should list the other node
kubectl -n piraeus-datastore exec deploy/linstor-controller -- linstor resource list-volumes
## other volume is now listed as in use
kubectl uncordon <node from above>
