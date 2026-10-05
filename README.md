Початкова ініцілізація, для розвертування ArgoCD

1. 
```bash
kubectl kustomize --enable-helm --load-restrictor LoadRestrictionsNone bootstrap/00-namespace | kubectl apply --server-side --force-conflicts -f -
```

2.
```bash
kubectl kustomize --enable-helm --load-restrictor LoadRestrictionsNone bootstrap/01-priority-classes | kubectl apply --server-side --force-conflicts -f -
```

3.
```bash
kubectl kustomize --enable-helm --load-restrictor LoadRestrictionsNone bootstrap/02-argo-cd | kubectl apply --server-side --force-conflicts -f -
```

4.
```bash
kubectl kustomize --enable-helm --load-restrictor LoadRestrictionsNone bootstrap/03-root-application | kubectl apply --server-side --force-conflicts -f -
```

[//]: # (2.)

[//]: # (```bash)

[//]: # (kubectl kustomize infrastructure/kustomize | kubectl apply -f -)

[//]: # (```)