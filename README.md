# Kubernetes Reloader Documentation

## Overview

Kubernetes Reloader is a tool used to automatically reload Kubernetes resources, such as Pods and Deployments, when a referenced ConfigMap or Secret is updated. This is particularly useful in scenarios where applications need to be reloaded to pick up new configurations without requiring manual intervention or restarting the Pods manually.

### Key Features:
- Watches for updates to ConfigMaps and Secrets.
- Automatically triggers a restart of Pods or Deployments that use the updated resources.
- Supports both deployment and statefulset controllers.
- Helps keep the environment up to date without manual intervention.

---

## Installation

Kubernetes Reloader can be installed in your cluster in a few simple steps.

### Prerequisites:
- A running Kubernetes cluster.
- `kubectl` installed and configured.
  
### Installation using Helm (Recommended)

1. **Add the Helm repository for Reloader:**
   
   ```bash
   helm repo add reloader https://stakater.github.io/reloader
   helm repo update
   ```

2. **Install Reloader using Helm:**
   
   ```bash
   helm install reloader reloader/reloader
   ```

   This will install the Reloader service in the default namespace. If you wish to install it in a specific namespace, you can use the `--namespace` flag.

### Installation using kubectl

1. **Download the latest release YAML file from GitHub:**
   
   ```bash
   kubectl apply -f https://github.com/stakater/Reloader/releases/download/vX.X.X/reloader.yaml
   ```

   Replace `vX.X.X` with the latest version.

---

## Usage

### Annotating Resources

Once Reloader is installed, you need to annotate your Deployments, StatefulSets, or DaemonSets to indicate which ConfigMap or Secret should trigger the reload. This is done using the following annotations:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  annotations:
    reloader.stakater.com/reload: "my-configmap"
spec:
  template:
    spec:
      containers:
        - name: my-app
          image: my-app-image
```

In the example above, the `my-app` Deployment is annotated to reload when the `my-configmap` ConfigMap changes.

### Supported Annotations

- **`reloader.stakater.com/reload`**: The name of the ConfigMap or Secret that should trigger the reload.
- **`reloader.stakater.com/reload-ignore`**: If set to `true`, prevents Reloader from automatically reloading when the referenced ConfigMap or Secret changes.

### Verifying Reloader is Running

After installation, you can verify that Reloader is running by checking the Reloader pods:

```bash
kubectl get pods -l app=reloader
```

If everything is working, you should see the Reloader pods in the output.

---

## Configuration

Reloader has a few configuration options that can be adjusted to suit your needs.

### 1. **Configuring Reloader to Watch Multiple Resources**

You can configure Reloader to watch multiple ConfigMaps or Secrets at once. Use the `reloader.stakater.com/reload` annotation to list multiple resources.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  annotations:
    reloader.stakater.com/reload: "configmap1,configmap2,secret1"
spec:
  template:
    spec:
      containers:
        - name: my-app
          image: my-app-image
```

### 2. **Using Reloader in Specific Namespaces**

You can configure Reloader to only watch resources in specific namespaces by setting the `namespace` value.

Example:
```bash
helm install reloader reloader/reloader --namespace my-namespace
```

This will install Reloader to the `my-namespace` namespace and only watch resources in that namespace.

### 3. **Customizing Reload Interval**

You can customize how often Reloader checks for updates to ConfigMaps or Secrets. This can be done by modifying the `reloader` deployment.

```bash
kubectl edit deployment reloader
```

Look for the `reload-interval` setting under the environment variables in the deployment, and modify it to your desired value.

---

## Troubleshooting

### Common Issues

1. **Pods Not Reloading After ConfigMap Update**
   - Ensure that the `reloader.stakater.com/reload` annotation is applied correctly to your Deployment, StatefulSet, or DaemonSet.
   - Confirm that the ConfigMap or Secret is correctly updated.

2. **Reloader Not Starting**
   - Verify that the Reloader pods are running using `kubectl get pods -l app=reloader`.
   - Check the logs of the Reloader pods using `kubectl logs <reloader-pod-name>` for any errors.

3. **Reloader Not Watching Resources in the Correct Namespace**
   - Ensure that the Reloader is installed in the same namespace as the resources you want to watch, or configure it to watch specific namespaces.

---

## Advanced Usage

### Using Reloader with Helm Charts

If you’re deploying applications using Helm, you can combine Helm with Kubernetes Reloader by using values from ConfigMaps or Secrets.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-helm-app
  annotations:
    reloader.stakater.com/reload: "my-configmap"
spec:
  template:
    spec:
      containers:
        - name: my-helm-app
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

Here, `my-configmap` will trigger a reload when it is updated, ensuring that your Helm-deployed application uses the latest configuration.

### Customizing Reload Behavior

Reloader allows you to configure the reload behavior when resources are updated. You can set Reloader to either:
- **Restart the Pods automatically**.
- **Trigger a rollout** of a Deployment/StatefulSet instead of restarting Pods directly.

This behavior can be controlled by adjusting the `reloader-stakater` deployment configuration as needed.

---

## Conclusion

Kubernetes Reloader simplifies the process of automatically reloading Pods and Deployments when their ConfigMap or Secret dependencies change. With its straightforward installation, easy configuration, and powerful features, Reloader helps automate updates and keeps your Kubernetes workloads in sync with configuration changes.

For more information, you can always visit the official [Kubernetes Reloader GitHub repository](https://github.com/stakater/Reloader).

---
```

You can now save this content in a `.md` file and use it as your documentation for Kubernetes Reloader! Let me know if you need anything else.
