# Підготовка
```
kubectl create secret generic vault-unseal-key -n vault --from-literal=key='<ваш_unseal_ключ>'
```