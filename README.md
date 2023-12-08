Certainly! Below is the provided documentation formatted as a Markdown file:

```markdown
# Deploying EKS Cluster Documentation

## EKS Cluster Deployment using Terraform

To deploy your EKS cluster, follow these steps:

1. Clone the Terraform project:
   ```bash
   git clone https://github.com/obinnaaliogor/eks.git
   cd eks
   ```

2. Initialize Terraform:
   ```bash
   terraform init
   ```

3. Format Terraform files:
   ```bash
   terraform fmt
   ```

4. Validate Terraform configuration:
   ```bash
   terraform validate
   ```

5. Plan the Terraform execution:
   ```bash
   terraform plan
   ```

6. Apply the Terraform configuration (auto-approve):
   ```bash
   terraform apply -auto-approve
   ```

7. Configure kubectl with the EKS cluster:
   ```bash
   aws eks update-kubeconfig --name demo --region us-east-1
   ```

8. View the user making API calls to AWS:
   ```bash
   aws sts get-caller-identity
   ```

9. Import AWS user credentials to kubeconfig:
   ```bash
   aws eks update-kubeconfig --name <cluster-name> --role-arn arn:aws:iam::account-id:role/IAM-Role-Name
   ```

10. Display AWS user credentials:
    ```bash
    cat $HOME/.aws/config
    cat $HOME/.aws/credentials
    ```

## Configuring Authentication in Amazon EKS Cluster

To configure authentication using AWS IAM roles, use the `aws-auth` ConfigMap:

```bash
kubectl get cm -n kube-system aws-auth
kubectl describe cm -n kube-system aws-auth
```

Add IAM Role ARN to `aws-auth` ConfigMap:

```bash
kubectl edit configmap -n kube-system aws-auth
```

Add the following section under `mapRoles`:

```yaml
- rolearn: <IAM-Role-ARN>
  username: <desired-username>
  groups:
    - system:masters
    - system:authenticated
```

Save the ConfigMap, apply changes, and verify configuration:

```bash
kubectl describe configmap -n kube-system aws-auth
```

## Authorization (RBAC) in Kubernetes

Role-Based Access Control (RBAC) is commonly used for authorization in Kubernetes. Here's a summary:

### Role Definition:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["create", "get", "watch", "list"]
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
```

### Role Binding:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Checking Permissions:

```bash
kubectl auth can-i get pods --as=my-desired-username --as-group=developers -n default
kubectl get pods --as=my-desired-username --as-group=developers -n default
```

### Testing Permissions as Another User:

```bash
kubectl auth can-i get pods --as=my-desired-username --as-group=developers -n default
kubectl auth can-i get pods --as=my-desired-username --as-group=developers -n default
```

### Limiting Access to Specific Resources:

```yaml
resourceNames: ["blue", "black"]
```

### Validation:

```bash
kubectl auth can-i get pods --as=my-desired-username --as-group=developers -n default
kubectl get pods --as=my-desired-username --as-group=developers -n default
```

## AWS STS AssumeRole Operations

Understanding `sts assumeRole` operations:

- Initial AssumeRole Operation
- Configuring Automatic Token Refresh
- SDK or CLI Handling Token Refresh
- Transparent Renewal for Applications
- IAM Role Policies Still Apply
- Rotation and Security Best Practices

## Testing AssumeRole:

```bash
aws eks update-kubeconfig --name=<clustername> --region=us-east-1 --profile <profilename>
aws sts assume-role --role-arn arn:aws:iam::61250xxxx:role/access-cluster-role --role-session-name my-test-session
aws eks update-kubeconfig --name=<clustername> --region=us-east-1 --role-arn=arn:aws:iam::612xxx:role/access-xxxx-role
```

## Reference

For additional information, refer to [AWS EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html).
```

You can copy and paste this Markdown content into a new file with the ".md" extension and upload it to your GitHub repository for documentation.