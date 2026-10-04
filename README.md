Початкова ініцілізація, для розвертування ArgoCD

1. 
```bash
kubectl kustomize --enable-helm --load-restrictor LoadRestrictionsNone bootstrap/ | kubectl apply --server-side --force-conflicts -f -
```

[//]: # (2.)

[//]: # (```bash)

[//]: # (kubectl kustomize infrastructure/kustomize | kubectl apply -f -)

[//]: # (```)