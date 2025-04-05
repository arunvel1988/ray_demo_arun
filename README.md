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



Sure! Here's a comprehensive **README** that explains the Tekton Task you’ve created for submitting Ray jobs using a Python script stored in a Kubernetes ConfigMap.

---

# Tekton Task: Submit Ray Job Using ConfigMap

This Tekton Task allows you to submit a **Ray job** to a Ray cluster using a Python script stored in a **ConfigMap**. The task is flexible, with the ability to specify the **Ray dashboard URL**, the **ConfigMap name**, the **ConfigMap key** (which holds the Python script), and the **Python Docker image** to use for executing the job.

## Features
- Submits a **Ray job** using a Python script stored in a **ConfigMap**.
- **Parameterizes** the Python Docker image, allowing you to specify different Python images (default is `python:3.9`).
- Directly submits the job to a Ray cluster using the provided **Ray dashboard URL**.
- The **Python script** is mounted from the **ConfigMap** and executed on the cluster.

## Prerequisites
- A **Ray cluster** should be up and running.
- A **ConfigMap** should be created in your Kubernetes cluster, containing the Python script for the Ray job.
- **Tekton Pipelines** should be installed and configured in your Kubernetes cluster.

## Configuration Parameters

The following parameters need to be specified when using this Tekton Task:

### Parameters

- `ray-dashboard-url` (required):  
  The URL of the **Ray dashboard**. Example: `http://raycluster-kuberay-head-svc.default.svc.cluster.local:8265`.

- `configmap-name` (required):  
  The name of the **ConfigMap** that contains the Python script.

- `configmap-key` (required):  
  The key in the **ConfigMap** where the Python script is stored.

- `python-image` (optional, default: `python:3.9`):  
  The **Docker image** used for running the Python code. Default is `python:3.9`, but you can specify a different image if needed.

## Resources

- **ConfigMap**: A Kubernetes ConfigMap that contains the Python script. It should include a key (e.g., `ray-script.py`) that holds the Python code to be executed.
- The **Python script** will be mounted from the ConfigMap and executed in the specified **Python Docker image**.

## Example ConfigMap

Here’s an example of how the **ConfigMap** containing the Python script should look:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ray-job-script
data:
  ray-script.py: |
    import ray
    ray.init()
    print("Ray cluster resources:")
    print(ray.cluster_resources())
```

## Example TaskRun

Below is an example `TaskRun` that uses the `submit-ray-job-from-configmap` task to submit a Ray job:

```yaml
apiVersion: tekton.dev/v1
kind: TaskRun
metadata:
  name: submit-ray-job-from-configmap-run
spec:
  taskRef:
    name: submit-ray-job-from-configmap
  params:
    - name: ray-dashboard-url
      value: http://raycluster-kuberay-head-svc.default.svc.cluster.local:8265
    - name: configmap-name
      value: ray-job-script
    - name: configmap-key
      value: ray-script.py
    - name: python-image
      value: python:3.9  # Optionally specify a custom Python image
  resources:
    - name: python-script
      resourceRef:
        name: ray-job-script  # The ConfigMap name
```

## How It Works

1. **Ray Installation**: The task installs Ray (`ray[default]`) inside the specified Python Docker image.
2. **Python Script**: The Python script stored in the **ConfigMap** is mounted and executed by the Python process.
3. **Ray Job Submission**: The job is submitted to the **Ray cluster** via the `ray job submit` command. The **Ray dashboard URL** is used to connect to the cluster.

## Task and TaskRun Workflow

### Step 1: Create the ConfigMap
Make sure to create a **ConfigMap** that contains your Python script. This script will be used for the Ray job.

### Step 2: Use the Task in a Pipeline
You can use the `submit-ray-job-from-configmap` task in a Tekton pipeline. The task can be customized by changing the parameters for the Ray cluster and Python image.

### Step 3: Create a TaskRun
Create a `TaskRun` that references the task and provides the necessary parameters, including the **Ray dashboard URL**, **ConfigMap name**, **ConfigMap key**, and **Python image** (optional).

## Notes

- The task uses the **Python image** specified in the `python-image` parameter, and it will install Ray inside the container.
- The Python script is mounted directly from the ConfigMap to avoid giving the Tekton task permissions to access the Kubernetes API directly.
- Make sure the **Ray dashboard URL** is correct and accessible from the Tekton Task.

## Conclusion

This Tekton Task provides a flexible and secure way to submit Ray jobs to your Ray cluster using Python scripts stored in Kubernetes ConfigMaps. It allows you to specify the Ray dashboard, Python script, and Python image dynamically.

For more information about Tekton Pipelines and configuring tasks, check out the official [Tekton documentation](https://tekton.dev/docs/).

---

Let me know if you need any additional details or changes!














