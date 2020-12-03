# ArgoCD + Renovate

Demonstrates how to use [Renovate]() to manage application versions deployed with [ArgoCD]().

[Renovate]: https://github.com/renovatebot/
[ArgoCD]: https://github.com/argoproj/argo-cd/

## Step 1. Fork this repository

## Step 2. Clone, replace references, and push

```bash
git clone https://github.com/my/my-fork.git
cd my-fork
```

Here's how you can identify remaining references.
With a little `sed`, you should be able to programmatically replace.

```
git grep "mjpitz/renovate-gitops" | grep -v README.md
```

Once all references have been replaced, be sure to commit and push your changes.
ArgoCD references what's on HEAD remotely, not what's locally.

```
git commit -a -m "replace references"
git push
```

## Step 2. Setup a Kind cluster

```bash
kind create cluster
```

## Step 3. Setup ArgoCD

```bash
kubectl apply -f namespaces/

helm dependency update workloads/argocd/
helm upgrade -i argocd ./workloads/argocd/ -n argocd
kubectl rollout status -n argocd deployment/argocd-server -w
```

## Step 4. Do the thing...

```
kubectl apply -f applications.yaml

watch kubectl get applications -A
```

## Step 6. Configure renovate and trigger a job

```bash
kubectl create secret generic \
    -n renovate renovate-secrets \
    --from-literal RENOVATE_TOKEN=access_token 

kubectl create job -n renovate --from cronjob/renovate renovate-$(date +%s)
```
