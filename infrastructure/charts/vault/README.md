# Підготовка

1. Зачекати, поки створиться под. vault-0 не запустться, бо secret немає

2. Створити 
```
kubectl create secret generic vault-unseal-key -n vault --from-literal=key='<ваш_unseal_ключ>'
```