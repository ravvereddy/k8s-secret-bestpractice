# k8s-secret-bestpractice

- In Kubernetes, secrets are not stored in the container filesystem but rather in the API server. They are securely transmitted to the nodes and then made available to the containers within the pods as environment variables or mounted volumes.

Here are the best practices for using secrets in Kubernetes, along with step-by-step examples:

# Avoid storing secrets in manifests:

  - It's recommended not to include sensitive information directly in Kubernetes manifests. Instead, use secrets to reference the sensitive data.

# Create a secret:

- Create a Kubernetes secret by either providing the secret data directly or referencing a file that contains the secret.

  - Example: Creating a secret with a key-value pair directly:

```shell
kubectl create secret generic my-secret --from-literal=username=admin --from-literal=password=secretpassword
```

  - Example: Creating a secret from a file:

```shell
kubectl create secret generic my-secret --from-file=secret-file.txt
```
# Mount a secret as a volume:

  - Mount the secret as a volume in a pod, allowing the containers within the pod to access the secret as files.

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-image
      volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret
  volumes:
    - name: secret-volume
      secret:
        secretName: my-secret
```        
- In this example, the my-secret secret is mounted at the path /etc/secret within the container.

# Use environment variables:

- Instead of mounting the secret as a volume, you can also inject the secret values as environment variables into your containers.

Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: my-image
      env:
        - name: MY_USERNAME
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: username
        - name: MY_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
```              
- In this example, the username and password keys from the my-secret secret are injected as environment variables within the container.

# Limit access to secrets:

- Apply proper RBAC (Role-Based Access Control) rules to restrict access to secrets. Ensure that only authorized users or services have access to the secrets.

# Encrypt etcd data at rest:

- Etcd, the key-value store used by Kubernetes, can be encrypted at rest to protect sensitive data, including secrets. Consult the Kubernetes documentation for instructions on enabling encryption for etcd.

- By following these best practices, you can effectively use secrets in Kubernetes to securely manage and distribute sensitive data to your containers. Remember to regularly rotate secrets, monitor access, and adhere to security policies specific to your organization.
