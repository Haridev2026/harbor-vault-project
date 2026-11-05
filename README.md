# harbor-vault-project# HashiCorp Vault & Harbor Setup

A hands-on DevOps project demonstrating secrets management using **HashiCorp Vault** and secure Docker image storage using **Harbor Container Registry**.

## 📌 Project Overview

This project demonstrates how to:

- Install and configure HashiCorp Vault.
- Initialize and unseal Vault.
- Enable KV v2 secrets engine.
- Create and manage Vault policies.
- Generate tokens with restricted permissions.
- Install and configure Harbor.
- Configure HTTPS/TLS for Harbor.
- Push and pull Docker images from Harbor.

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| HashiCorp Vault | Secrets management |
| Harbor | Private container registry |
| Docker | Container management |
| Linux | Operating system |
| OpenSSL | TLS certificate generation |
| Git & GitHub | Version control |

## 📂 Project Structure

```text
vault-harbor-project/
│
├── vault/
│   ├── policies/
│   │   ├── app-policy.hcl
│   │   └── developer-policy.hcl
│   └── README.md
│
├── harbor/
│   ├── harbor.yml.example
│   └── README.md
│
└── README.md
```

## 🔐 HashiCorp Vault Configuration

### 1. Check Vault Status

```bash
vault status
```

### 2. Enable KV v2 Secrets Engine

```bash
vault secrets enable -path=secret kv-v2
```

### 3. Store a Secret

```bash
vault kv put secret/app/config \
  username="appuser" \
  password="CHANGE_ME"
```

### 4. Read a Secret

```bash
vault kv get secret/app/config
```

### 5. Create a Read-Only Policy

Create `developer-policy.hcl`:

```hcl
path "secret/data/app/*" {
  capabilities = ["read"]
}

path "secret/metadata/app" {
  capabilities = ["list"]
}

path "secret/metadata/app/*" {
  capabilities = ["list"]
}
```

Apply the policy:

```bash
vault policy write developer-policy developer-policy.hcl
```

### 6. Generate a Developer Token

```bash
vault token create \
  -policy="developer-policy" \
  -ttl="1h"
```

### 7. Verify Token Permissions

```bash
vault token capabilities secret/data/app/config
```

Expected result:

```text
read
```

## 🐳 Harbor Container Registry

### 1. Configure Harbor

Update `harbor.yml` with your hostname and HTTPS certificate paths.

Example:

```yaml
hostname: harbor.example.com

https:
  port: 443
  certificate: /path/to/harbor.crt
  private_key: /path/to/harbor.key
```

### 2. Deploy Harbor

```bash
./prepare
docker compose up -d
```

### 3. Check Harbor Containers

```bash
docker compose ps
```

### 4. Login to Harbor

```bash
docker login harbor.example.com
```

### 5. Tag and Push an Image

```bash
docker pull nginx:latest

docker tag nginx:latest \
  harbor.example.com/library/nginx:latest

docker push harbor.example.com/library/nginx:latest
```

### 6. Pull an Image from Harbor

```bash
docker pull harbor.example.com/library/nginx:latest
```

## 🔒 Security Best Practices

- Never commit Vault root tokens to GitHub.
- Never commit passwords, API keys, or private keys.
- Keep TLS certificates and private keys outside the repository.
- Use `.gitignore` to exclude sensitive files.
- Apply least-privilege Vault policies.
- Use short-lived tokens wherever possible.
- Store actual secrets in Vault, not in GitHub.

## 🚫 Git Ignore

Example `.gitignore`:

```gitignore
# Vault secrets and credentials
.env
*.token
*.secret
vault-token
root-token

# TLS private keys and certificates
*.key
*.pem
*.crt

# Harbor configuration containing environment-specific settings
harbor.yml

# Logs
*.log
```

Commit a sanitized `harbor.yml.example` instead of your actual configuration.

## 📸 Screenshots

Add screenshots of:

- Vault status (with sensitive information hidden).
- Vault KV secrets engine.
- Vault policy configuration.
- Harbor dashboard.
- Docker image repositories.

## 🎯 Learning Outcomes

- Understanding secrets management with HashiCorp Vault.
- Implementing role-based access through Vault policies and tokens.
- Configuring a private Docker registry using Harbor.
- Understanding TLS certificates and HTTPS.
- Managing Docker images securely.

## 👨‍💻 Author

Your Name

GitHub: https://github.com/YOUR_USERNAME

---

**Disclaimer:** This repository is for learning and demonstration purposes. Do not use sample credentials or development TLS configurations in production.
