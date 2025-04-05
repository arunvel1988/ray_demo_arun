# ray_demo_arun
helm repo add kuberay https://ray-project.github.io/kuberay-helm/
helm repo update

# Install both CRDs and KubeRay operator v1.3.0.
helm install kuberay-operator kuberay/kuberay-operator --version 1.3.0

# Confirm that the operator is running in the namespace `default`.
kubectl get pods
# NAME                                READY   STATUS    RESTARTS   AGE
# kuberay-operator-7fbdbf8c89-pt8bk   1/1     Running   0          27s


helm install raycluster kuberay/ray-cluster --version 1.3.0

kubectl get rayclusters


kubectl get pods --selector=ray.io/cluster=raycluster-kuberay









