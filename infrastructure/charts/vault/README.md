# Підготовка

1. Зачекати, поки створиться под. vault-0 не запустться, бо secret немає

2. Створимо відповідний token

```bash
kubectl exec -n vault -ti vault-0 -- vault operator init -key-shares=1 -key-threshold=1
```

3. Створити secret, з відповідним значенням unseal-key
```bash
kubectl create secret generic vault-unseal-key -n vault --from-literal=key='<unseal_key>'
```