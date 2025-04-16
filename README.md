# Kind

- initial cluster

```
kind create cluster --config kind-config.yaml
kind delete cluster --fullcycle
```

## commads k8s

- Observar o hpa

```md
# watch -n1 kubectl get hpa
```

### Aplicar arquivos de servicos

```md
- kubectl apply -f k8s/deployment.yaml
- kubectl apply -f k8s/service.yaml
- kubectl apply -f k8s/secret.yaml
- kubectl apply -f k8s/hpa.yaml
- kubectl apply -f k8s/configmap-members.yaml
- kubectl apply -f k8s/metrics-services.yaml
- kubectl apply -f k8s/pvc.yaml
- kubectl apply -f k8s/statefulset
```

### Principais comandos kubectl

```md
- kubectl get pods
- kubectl get nodes
- kubectl get services
- kubectl get apiservices
- kubectl get storageclass
- kubectl get pvc
- kubectl get statefulset
- kubectl get deployment

- kubectl delete deployment nodeserver
- kubectl delete statefulset mystatefulset

- kubectl top pod nodeserver-7648c5665c-7jcck
- kubectl describe pod nodeserver-86cfcd9958-d8cgf
- kubectl logs nodeserver-86cfcd9958-d8cgf

- kubectl config get-contexts
- kubectl config use-contexts kind-fullcly
```

## Scalando o statefulset na mao

```
kubectl scale statefulset "name_statefulset" --replicas=5
```

### Principais comandos kind

```md
- kind create cluster --config k8s/kind.yaml --name=fullcycle
```

### Adicionar o metrics services no kind

```md
- wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

aplicar no spec do deploymente
spec:
containers: - args: - --kubelet-insecure-tls
```

### Port foward

```
kubectl port-forward pods/frontend-778979c958-cvfws  9000:5000 -n default
```

### Transformar base64

```
 echo "123456" | base64 -- base decoder
```

# Argocd

- Create argocd services and namespace

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

- Visualize all argo services

```
kubectl get all -n argocd
kubectl get all svc -n argocd
kubectl logs -n argocd deploy/argocd-server

```

- Service Type Load Balancer

```
kubectl edit svc/argocd-server -n argocd
or
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

- Port Forwarding

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

- Get initial argo user and password

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

admin
admin123

- Create ssh-keygen

```bash
ssh-keygen -t rsa -b 4096 -C "seu_email@exemplo.com" -f ~/.ssh/nome_da_chave_rsa
```

- Para usar a chave SSH que você criou, o processo geralmente envolve o uso do ssh-agent e a adição da sua chave privada ao agente

```bash
code ~/.bashrc

eval "$(ssh-agent -s)"
source ~/.bashrc

ssh-add ~/.ssh/minha_chave_rsa
```

### ARGO LOGIN

```bash
argocd login localhost:8080 --username <SEU_USUARIO> --password <SUA_SENHA> --insecure
argocd repo add git@github.com:minhaorganizacao/meu-projeto.git --ssh-private-key-path ~/.ssh/argo-repo-key
argocd repo add git@github.com:gilsonrusso/argocd-trainning.git --ssh-private-key-path ~/.ssh/argocd_rsa
```