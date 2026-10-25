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

# Підключення tailscale-operator (Vault Secrets Operator)

Маніфести в `infrastructure/secrets/tailscale-operator/` (VaultConnection, VaultAuth,
VaultStaticSecret) вже описують, звідки VSO має забрати секрет, але сам Vault
треба один раз налаштувати вручну — KV engine, kubernetes auth, policy, role
і сам пароль не живуть у git.

Залогінившись у Vault (root token з кроку 2), виконати:

```bash
# 1. Увімкнути KV v2 на mount "secret" (якщо ще не увімкнено)
kubectl exec -n vault -ti vault-0 -- vault secrets list
kubectl exec -n vault -ti vault-0 -- vault secrets enable -path=secret -version=2 kv

# 2. Увімкнути kubernetes auth method (mount "kubernetes", як у vault-auth.yaml)
kubectl exec -n vault -ti vault-0 -- vault auth enable kubernetes

# 3. Налаштувати auth method на in-cluster API
kubectl exec -n vault -ti vault-0 -- vault write auth/kubernetes/config \
  kubernetes_host="https://kubernetes.default.svc"

# 4. Policy, що дозволяє читати лише потрібний секрет
kubectl exec -n vault -ti vault-0 -- vault policy write tailscale-operator - <<'EOF'
path "secret/data/tailscale-operator/oauth" {
  capabilities = ["read"]
}
EOF

# 5. Роль, прив'язана до SA tailscale-oauth-vault-auth у ns tailscale
kubectl exec -n vault -ti vault-0 -- vault write auth/kubernetes/role/tailscale-operator \
  bound_service_account_names=tailscale-oauth-vault-auth \
  bound_service_account_namespaces=tailscale \
  policies=tailscale-operator \
  ttl=1h

# 6. Сам секрет (OAuth client з адмінки Tailscale: Settings → OAuth clients)
kubectl exec -n vault -ti vault-0 -- vault kv put secret/tailscale-operator/oauth \
  client_id=<CLIENT_ID> client_secret=<CLIENT_SECRET>
```

Після цього VSO залогіниться під роллю `tailscale-operator`, створить Secret
`operator-oauth` у namespace `tailscale`, і под оператора його підхопить
(якщо под вже встиг впасти в CrashLoop через відсутній Secret — перестворити
под вручну).