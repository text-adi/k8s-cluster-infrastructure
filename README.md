Початкова ініцілізація, для розвертування ArgoCD

1. 
```bash
helmfile apply -f bootstrap/helmfile.yaml
```

[//]: # (2.)

[//]: # (```bash)

[//]: # (kubectl kustomize infrastructure/kustomize | kubectl apply -f -)

[//]: # (```)