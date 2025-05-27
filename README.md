# Keycloak + OpenLDAP on Kubernetes (K3d) using Helm

This repository contains a ready-to-deploy configuration for running **Keycloak integrated with OpenLDAP** on a lightweight **K3d Kubernetes cluster**. It uses **Bitnami Helm Charts** and supports **automatic realm import**.

---

## 📁 Project Structure

```
kpc-keycloak-ldap/
├── realm/
│   └── myrealm.json            # Realm to import into Keycloak
├── keycloak-values.yaml        # Helm values for Keycloak
└── openldap-values.yaml        # Helm values for OpenLDAP
```

---

## ⚙️ Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [K3d](https://k3d.io/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)

---

## 🚀 Deployment Steps

### 1. Create K3d Cluster

```bash
k3d cluster create keycloak-cluster \
  --agents 2 \
  --port "8080:80@loadbalancer" \
  --port "1389:1389@loadbalancer" \
  --wait
```

### 2. Add Bitnami Helm Repo

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### 3. Deploy OpenLDAP

```bash
helm install openldap bitnami/openldap -f openldap-values.yaml
```

### 4. Create ConfigMap for Realm

```bash
kubectl create configmap keycloak-realm --from-file=realm/myrealm.json
```

### 5. Deploy Keycloak

```bash
helm install keycloak bitnami/keycloak -f keycloak-values.yaml
```

---

## 🌐 Access Keycloak

Visit [http://localhost:8080](http://localhost:8080)

- Username: `admin`
- Password: `admin`
- Realm imported: `MyRealm`

---

## 🧪 Configure LDAP in Keycloak

1. Go to `User Federation` → `Add Provider` → `ldap`
2. Fill in the form:

| Field             | Value                                              |
|------------------|-----------------------------------------------------|
| Vendor           | Other                                               |
| Connection URL   | `ldap://openldap.default.svc.cluster.local:1389`    |
| Bind DN          | `cn=admin,dc=example,dc=org`                         |
| Bind Credential  | `admin123`                                          |
| Users DN         | `dc=example,dc=org`                                 |

3. Click **Test Connection** and **Test Authentication**

---

## 🧹 Cleanup

To delete all:

```bash
k3d cluster delete keycloak-cluster
```

---

## 📄 License

MIT
