# 🚀 Projeto: ArgoCD com Helm e Kind

Este projeto demonstra como levantar um ambiente local com [Kind](https://kind.sigs.k8s.io/), instalar o [ArgoCD](https://argo-cd.readthedocs.io/), e fazer deploy de aplicações Helm com CI/CD GitOps.

---

## 🐳 Pré-requisitos

- [Docker](https://www.docker.com/)
- [Kind](https://kind.sigs.k8s.io/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [ArgoCD CLI](https://argo-cd.readthedocs.io/en/stable/cli_installation/)
- Git + Chave SSH configurada

---

## ⚙️ Criando o Cluster com Kind

```bash
kind create cluster --config kind-config.yaml --name fullcycle
```

> Para deletar o cluster:

```bash
kind delete cluster --name fullcycle
```

---

## 📦 Instalando o ArgoCD

```bash
kubectl create namespace argocd

kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Visualizar os serviços do ArgoCD

```bash
kubectl get all -n argocd
kubectl logs -n argocd deploy/argocd-server
```

### Liberar acesso com Port Forward

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

> Agora você pode acessar o painel do ArgoCD: [http://localhost:8080](http://localhost:8080)

---

## 🔐 Acessando o ArgoCD

### Usuário e senha padrão

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d
```

> Usuário padrão: `admin`

### Login via CLI

```bash
argocd login localhost:8080 --username admin --password <senha> --insecure
```

---

## 🔑 Configurando chave SSH

1. Gere uma chave SSH:

```bash
ssh-keygen -t rsa -b 4096 -C "seu_email@exemplo.com" -f ~/.ssh/argo-repo-key
```

2. Adicione a chave ao ssh-agent:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/argo-repo-key
```

3. Adicione a chave pública no GitHub em **Settings > SSH and GPG keys**

---

## 🔗 Conectando repositório Git ao ArgoCD

```bash
argocd repo add git@github.com:gilsonrusso/argocd-trainning.git \
  --ssh-private-key-path ~/.ssh/argo-repo-key
```

---

## 🚀 Criando Aplicação no ArgoCD

```bash
argocd app create masterizando \
  --repo git@github.com:gilsonrusso/argocd-trainning.git \
  --path masterizando \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

---

## 🌐 Instalando o Ingress Controller (NGINX)

Para acessar sua aplicação via Ingress (sem usar `kubectl port-forward`), instale o NGINX Ingress Controller no cluster:

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace \
  --set controller.hostNetwork=true \
  --set controller.kind=DaemonSet \
  --set controller.service.type=NodePort
```

> Certifique-se de que seu `kind.yaml` esteja mapeando as portas 80 e 443 para o host.

---

## 📈 Instalando o Metrics Server (para HPA)

```bash
wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

> Edite o arquivo para permitir TLS inseguro:

```yaml
spec:
  containers:
    - args:
        - --kubelet-insecure-tls
```

```bash
kubectl apply -f components.yaml
```

---

## 🔍 Comandos Úteis

### ⚙️ Monitorando recursos no cluster

```bash
watch -n1 kubectl get hpa
kubectl get pods
kubectl top pod
kubectl describe pod <nome>
kubectl logs <nome>
```

### 📦 Aplicando recursos do Kubernetes

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/secret.yaml
kubectl apply -f k8s/hpa.yaml
kubectl apply -f k8s/configmap-members.yaml
kubectl apply -f k8s/metrics-services.yaml
kubectl apply -f k8s/pvc.yaml
kubectl apply -f k8s/statefulset.yaml
```

### ⬆️ Escalando recursos manualmente

```bash
kubectl scale statefulset <nome> --replicas=3
```

---

## 🧪 Port Forward para Debug

```bash
kubectl port-forward pods/frontend-xxxxxxx-xxxxx 9000:5000 -n default
```

---

## 🧰 Extras

### 🔐 Codificando valores em base64 (para Secrets)

```bash
echo "123456" | base64
```

---
