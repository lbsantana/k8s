# Kubernetes Labs – Blue-Green Deployment com Ingress

Este laboratório demonstra uma estratégia de **blue-green deployment** utilizando **Kubernetes**, **Ingress Controller** e uma aplicação simples em HTML para visualização do tráfego entre versões.

A aplicação possui duas versões:
- **web-green** → versão estável
- **web-blue** → versão candidata

As páginas exibem o **pod** que processou a requisição, permitindo visualizar balanceamento, atualização e testes entre versões.

---

# 📌 Objetivo do Projeto

- Demonstrar um fluxo real de **deploy Kubernetes** com duas versões da aplicação.
- Utilizar **Ingress** para rotear tráfego baseado em path (`/` e `/blue`).
- Entender conceitos de:
  - Blue-Green Deployment
  - Ingress Controller
  - Serviços e Deployments
  - Namespaces e organização de cluster
- Servir como base para pipelines **CI/CD**, observabilidade e boas práticas DevOps.

---

# 🧱 Arquitetura da Solução

Componentes utilizados:

- Cluster Kubernetes local (**kind**)
- **Ingress Controller – ingress-nginx**
- Deployments:
  - `web-green`
  - `web-blue`
- Service:
  - `web-service`
- Ingress para expor rotas:
  - `/` → versão **green**
  - `/blue` → versão **blue**
- Página HTML identifica qual pod atendeu a requisição.

Fluxo simplificado:

```
Usuário → Ingress Controller → Service → Pods (green/blue)
```

Cada página responde com algo como:

```
Servidor que processou a requisição - web-green-5cdf45768b-7fkmc
```

---

# 🗂 Estrutura de Pastas

```
k8s/
 └── labs/
      ├── blue-green-deployment.yaml
      ├── ingress.yaml
      ├── ingress-controller-installation.yaml
      ├── kind-config.yaml
      ├── Annotations.txt
      └── (outros arquivos adicionados futuramente)
```

---

# ⚙️ Pré-requisitos

- **kubectl** instalado
- **Docker**
- **kind** ou **minikube**
- Acesso local ao cluster
- Opcional:
  - Domínio configurado (ex.: `barbosadevops.com.br`)
  - Registry (Docker Hub, ECR, etc.)

---

# 🚀 Criando o Cluster KIND (opcional)

```bash
kind create cluster --config kind-config.yaml
```

---

# 🌐 Instalando o Ingress Controller (ingress-nginx)

```bash
kubectl apply -f ingress-controller-installation.yaml
kubectl get pods -n ingress-nginx
```

---

# 📦 Deploy da Aplicação (Green & Blue)

```bash
kubectl apply -f deployment.yaml
kubectl get pods -o wide
kubectl get svc
```

---

# 🌍 Criando o Ingress da Aplicação

```bash
kubectl apply -f ingress.yaml
kubectl get ingress
```

---

# 🧪 Testando no Navegador

### Local:
- http://localhost  
- http://localhost/blue  

### Domínio:
- http://barbosadevops.com.br  
- http://barbosadevops.com.br/blue  

---

# 🔄 Estratégia Blue-Green Deployment

- `/` → **green**
- `/blue` → **blue**

Para promover blue → green:
- Edite o ingress apontando `/` para o service blue.

---

# 🔍 Observabilidade

```bash
kubectl logs <pod>
kubectl describe ingress web-ingress
kubectl get pods -o wide
```

---

# 🔐 Boas Práticas

- Adicionar **requests/limits**
- Usar **probes**
- TLS com cert-manager (futuro)
- Evitar containers root

---

# 🤖 CI/CD (Sugestão)

```yaml
- name: Deploy to cluster
  run: |
    kubectl apply -f k8s/labs/deployment.yaml
    kubectl apply -f k8s/labs/ingress.yaml
```

---

# 🧑‍💻 Sobre Este Lab

Faz parte da minha trilha DevOps, com foco em:
- Kubernetes
- Ingress
- Estratégias de release
- CI/CD
- Clusters locais com kind/minikube
