Here’s a ready-to-use **README.md** file you can drop into a GitHub repo 👇

---

````markdown
# GCP CLI & GKE Access – Quickstart (macOS & Linux)

This guide explains how to install the **Google Cloud CLI**, authenticate, set your project/region/zone, and connect to a **GKE** cluster using `kubectl`.

---

## Prerequisites

- A Google account with access to your GCP project
- IAM permissions (ask your admin if unsure):
  - **Project**: `roles/viewer` (read) or `roles/editor` (write)
  - **GKE**: `roles/container.clusterViewer` (read) or `roles/container.developer` (operate)
  - Proper **Kubernetes RBAC** bindings inside the cluster

---

## 1. Install the Google Cloud SDK (gcloud)

### macOS (Homebrew)
```bash
brew install --cask google-cloud-sdk

# Add gcloud to your shell
echo 'source "/opt/homebrew/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/path.zsh.inc"' >> ~/.zshrc
echo 'source "/opt/homebrew/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/completion.zsh.inc"' >> ~/.zshrc

exec -l $SHELL
gcloud version
````

### Linux – Debian/Ubuntu (APT)

```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates gnupg
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" \
| sudo tee /etc/apt/sources.list.d/google-cloud-sdk.list

sudo apt-get update && sudo apt-get install -y google-cloud-cli
gcloud version
```

### Linux – RHEL/CentOS/Fedora

```bash
sudo tee -a /etc/yum.repos.d/google-cloud-sdk.repo >/dev/null <<'EOF'
[google-cloud-cli]
name=Google Cloud CLI
baseurl=https://packages.cloud.google.com/yum/repos/cloud-sdk-el8-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
       https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

sudo yum install -y google-cloud-cli || sudo dnf install -y google-cloud-cli
gcloud version
```

### kubectl (if not already installed)

```bash
# macOS
brew install kubectl

# Debian/Ubuntu
sudo apt-get install -y kubectl

# RHEL/CentOS/Fedora
sudo yum install -y kubectl || sudo dnf install -y kubectl

kubectl version --client
```

---

## 2. Authenticate

### Login with browser

```bash
gcloud auth login
```

### Headless login (no auto browser)

```bash
gcloud auth login --no-launch-browser
```

### Configure defaults

```bash
PROJECT_ID="your-project-id"
REGION="us-central1"
ZONE="us-central1-a"

gcloud config set project "$PROJECT_ID"
gcloud config set compute/region "$REGION"
gcloud config set compute/zone "$ZONE"
```

> List available projects:

```bash
gcloud projects list
```

---

## 3. Enable Required APIs

```bash
gcloud services enable container.googleapis.com
```

---

## 4. Get Access to a GKE Cluster

### List clusters

```bash
gcloud container clusters list --region "$REGION"
```

### Get kubeconfig credentials

```bash
CLUSTER_NAME="your-gke-cluster"
gcloud container clusters get-credentials "$CLUSTER_NAME" --region "$REGION"
```

### Verify access

```bash
kubectl config get-contexts
kubectl get nodes
kubectl get ns
```

---

## 5. Service Account Authentication (CI/CD)

> Prefer **Workload Identity** over keys for production.

```bash
SA_EMAIL="ci-bot@${PROJECT_ID}.iam.gserviceaccount.com"
KEY_PATH="/path/to/key.json"

gcloud auth activate-service-account "$SA_EMAIL" --key-file="$KEY_PATH"
gcloud config set project "$PROJECT_ID"
gcloud auth list
```

---

## 6. Common Commands

```bash
# Switch project
gcloud config set project another-project

# Check identity
gcloud auth list

# Update gcloud
gcloud components update

# Test cluster access
kubectl get deploy -A
```

---

## 7. Troubleshooting

* **Forbidden / PERMISSION_DENIED** → Check IAM roles & RBAC.
* **Not authorized** → Run `gcloud container clusters get-credentials` again.
* **Timeouts** → Ensure VPN/network connectivity to control plane.
* **Multiple Google accounts** → Run:

  ```bash
  gcloud auth list
  gcloud config set account you@company.com
  ```

---

## 8. Quick Setup Script

```bash
#!/usr/bin/env bash
set -euo pipefail

PROJECT_ID="your-project-id"
REGION="us-central1"
ZONE="us-central1-a"
CLUSTER_NAME="your-gke-cluster"

gcloud config set project "$PROJECT_ID"
gcloud config set compute/region "$REGION"
gcloud config set compute/zone "$ZONE"

gcloud services enable container.googleapis.com

gcloud auth login --brief

gcloud container clusters get-credentials "$CLUSTER_NAME" --region "$REGION"

kubectl get nodes
kubectl config current-context
```

---

## 9. Security Best Practices

* Use **Workload Identity** instead of JSON keys.
* Apply **least privilege** IAM roles.
* Rotate and clean up unused credentials.
* Never commit kubeconfigs or keys to git.

```

---

Do you want me to also generate a **`setup.sh`** script alongside the README so users can just run it after cloning your GitHub repo?
```
GCP CLI & GKE Access – Quickstart (macOS & Linux)

A copy-paste friendly README to:

    install the gcloud CLI,

    authenticate,

    set your project/region/zone,

    and connect to a GKE cluster with kubectl.

Prereqs

    A Google account with access to your org/project

    IAM permissions (ask your admin if unsure):

        Project: roles/viewer (read) or roles/editor (write)

        GKE: roles/container.clusterViewer (read) or roles/container.developer (operate)

        Plus any in-cluster RBAC (Kubernetes RoleBinding/ClusterRoleBinding) tied to your identity or group

1) Install the Google Cloud SDK (gcloud)
macOS (Homebrew)

# If you don't have Homebrew: https://brew.sh
brew install --cask google-cloud-sdk

# Add gcloud to your shell (if not auto-done)
# Zsh:
echo 'source "/opt/homebrew/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/path.zsh.inc"' >> ~/.zshrc
echo 'source "/opt/homebrew/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/completion.zsh.inc"' >> ~/.zshrc
# Bash:
echo 'source "/opt/homebrew/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/path.bash.inc"' >> ~/.bashrc
echo 'source "/opt/homebrew/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/completion.bash.inc"' >> ~/.bashrc

exec -l $SHELL
gcloud version

Linux – Debian/Ubuntu (APT)

sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates gnupg
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" \
| sudo tee /etc/apt/sources.list.d/google-cloud-sdk.list

sudo apt-get update && sudo apt-get install -y google-cloud-cli
gcloud version

Linux – RHEL/CentOS/Fedora (YUM/DNF)

# RHEL/CentOS
sudo tee -a /etc/yum.repos.d/google-cloud-sdk.repo >/dev/null <<'EOF'
[google-cloud-cli]
name=Google Cloud CLI
baseurl=https://packages.cloud.google.com/yum/repos/cloud-sdk-el8-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
       https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

sudo yum install -y google-cloud-cli || sudo dnf install -y google-cloud-cli
gcloud version

kubectl (if not already present)

# macOS
brew install kubectl

# Debian/Ubuntu
sudo apt-get install -y kubectl

# RHEL/CentOS/Fedora
sudo yum install -y kubectl || sudo dnf install -y kubectl

kubectl version --client

2) Authenticate
Interactive login (opens browser)

gcloud auth login

Headless/remote shells (no browser auto-launch)

gcloud auth login --no-launch-browser
# copy the URL to a local browser, paste the code back

Set your default project/region/zone

# Replace with your values
PROJECT_ID="your-project-id"
REGION="us-central1"
ZONE="us-central1-a"

gcloud config set project "$PROJECT_ID"
gcloud config set compute/region "$REGION"
gcloud config set compute/zone "$ZONE"

gcloud config list

    🔎 List projects you can access:

gcloud projects list

(Optional) Application Default Credentials (ADC)

Some SDKs/tools use ADC:

gcloud auth application-default login

3) Enable Required APIs (once per project)

gcloud services enable container.googleapis.com
# Add others as needed:
# gcloud services enable compute.googleapis.com iam.googleapis.com

4) Get Access to a GKE Cluster
Discover clusters

# In a region:
gcloud container clusters list --region "$REGION"
# In a zone:
gcloud container clusters list --zone "$ZONE"

Fetch kubeconfig for your cluster

CLUSTER_NAME="your-gke-cluster"

# Regional cluster:
gcloud container clusters get-credentials "$CLUSTER_NAME" --region "$REGION"

# Zonal cluster (alternative):
# gcloud container clusters get-credentials "$CLUSTER_NAME" --zone "$ZONE"

This creates/updates your ~/.kube/config with a context like:

gke_${PROJECT_ID}_${REGION}_${CLUSTER_NAME}

Verify cluster access

kubectl config get-contexts
kubectl config current-context
kubectl get nodes
kubectl get ns

If you see RBAC errors (Forbidden), ask your admin to bind your Google identity/group to the proper Kubernetes roles in that cluster.
5) Service Account Auth (CI/CD or Automation)
Activate a service account with a key (local testing)

    Prefer Workload Identity in GKE for production. Keys are risky.

SA_EMAIL="ci-bot@${PROJECT_ID}.iam.gserviceaccount.com"
KEY_PATH="/path/to/key.json"

gcloud auth activate-service-account "$SA_EMAIL" --key-file="$KEY_PATH"
gcloud config set project "$PROJECT_ID"

# Verify identity:
gcloud auth list

GKE with Workload Identity (recommended)

    Bind a Google service account to a Kubernetes service account via Workload Identity.

    Your Pods assume the Google SA identity without JSON keys.

    Ask your admin for the project’s Workload Identity pool and binding procedure.

6) Common Tasks

# Switch project/region/zone
gcloud config set project another-project
gcloud config set compute/region europe-west1
gcloud config set compute/zone europe-west1-b

# Re-auth if token expired
gcloud auth login

# Update gcloud components (legacy installs)
gcloud components update

# Check who you are
gcloud auth list

# Test kubectl against cluster
kubectl get deploy -A

7) Troubleshooting

    PERMISSION_DENIED / Forbidden
    Ensure you have the right IAM roles (project + GKE) and Kubernetes RBAC.

    Not authorized to access GKE cluster
    Run gcloud container clusters get-credentials ... again for the correct region/zone.

    kubectl hangs or times out
    Verify network/VPN to the control plane and that the cluster is RUNNING:

gcloud container clusters describe "$CLUSTER_NAME" --region "$REGION" --format='value(status)'

Multiple Google accounts
Use gcloud auth login --brief and check gcloud auth list. Set the active account:

    gcloud config set account you@company.com

8) Copy-Paste Setup Script (edit values, then run)

#!/usr/bin/env bash
set -euo pipefail

# ===== EDIT THESE =====
PROJECT_ID="your-project-id"
REGION="us-central1"
ZONE="us-central1-a"
CLUSTER_NAME="your-gke-cluster"
# ======================

echo "[1/5] Configuring gcloud..."
gcloud config set project "$PROJECT_ID"
gcloud config set compute/region "$REGION"
gcloud config set compute/zone "$ZONE"

echo "[2/5] Enabling GKE API..."
gcloud services enable container.googleapis.com

echo "[3/5] Authenticating (browser may open)..."
gcloud auth login --brief

echo "[4/5] Getting GKE credentials..."
gcloud container clusters get-credentials "$CLUSTER_NAME" --region "$REGION"

echo "[5/5] Verifying cluster access..."
kubectl get nodes
kubectl get ns

echo "✅ All set! Current context:"
kubectl config current-context

9) Security Tips

    Prefer Workload Identity over JSON keys for GKE workloads.

    Use least-privilege IAM roles (container.clusterViewer vs container.admin).

    Rotate credentials and remove unused accounts/keys.

    Avoid committing kubeconfigs or service account keys to git.
