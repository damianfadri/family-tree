# Family Tree
## Overview
Family Tree is a basic Kubernetes deployment setup for [Webtrees](https://github.com/fisharebest/webtrees), an open source genealogy application.

## Prerequisites
* [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?pivots=winget)
* [kubectl](https://kubernetes.io/docs/reference/kubectl/)

## Running the Project
1. Connect to your cluster. For example, in Azure Kubernetes Service,
```bash
az login
az aks get-credentials -g <resource-group> -n <aks-name>

kubectl config use-context <aks-name>
```

2. Create a `v1.Secret` named `webtrees-secrets`.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: webtrees-secrets
type: Opaque
stringData:
  DB_TYPE: "mysql"
  DB_HOST: "mariadb"
  DB_PORT: "3306"
  DB_USER: "<db-user>"
  DB_PASS: "<db-user-pass>"
  DB_NAME: "<db-name>"
  DB_PREFIX: "<db-prefix>_"
  WT_USER: "<webtrees-user>"
  WT_PASS: "<webtrees-user-pass>"
  WT_NAME: "<webtrees-user-name>"
  WT_EMAIL: "<webtrees-user-email>"
```

3. Create a `v1.Secret` named `mariadb-secrets`.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mariadb-secrets
type: Opaque
stringData:
  MARIADB_DATABASE: "<db-name>"
  MARIADB_USER: "<db-user>"
  MARIADB_PASSWORD: "<db-user-pass>"
  MARIADB_ROOT_PASSWORD: "<db-root-pass>"
```

4. Apply the manifests.
```bash
kubectl apply -f . -R
```

5. Wait for deployments to be ready.
```bash
kubectl wait --for=condition=ready pod --selector=app=webtrees --timeout=120s
kubectl wait --for=condition=ready pod --selector=app=mariadb --timeout=120s
```