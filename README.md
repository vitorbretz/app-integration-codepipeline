# Jenkins K8S Platform

Plataforma DevOps completa rodando em cluster Kubernetes local (Kind) com Jenkins, Gitea, Harbor, SonarQube e ArgoCD.

## 🚀 Serviços Disponíveis

- **Jenkins** - CI/CD Pipeline
- **Gitea** - Git Server
- **Harbor** - Container Registry
- **SonarQube** - Code Quality
- **ArgoCD** - GitOps Deployment

## 📋 Pré-requisitos

- Docker
- Kind
- Kubectl
- Helm
- Helmfile

## 🛠️ Instalação

### 1. Criar o cluster Kind

```bash
make up
```

Este comando irá:
- Criar o cluster Kind com 3 nodes (1 control-plane + 2 workers)
- Instalar MetalLB para LoadBalancer
- Instalar todos os serviços via Helmfile

### 2. Configurar acesso aos serviços

Execute o script para atualizar o `/etc/hosts`:

```bash
./update-hosts.sh
```

## 🌐 Acessando os Serviços

Após a instalação, acesse os serviços diretamente pelo navegador:

- **Jenkins**: http://jenkins.localhost.com
- **Gitea**: http://gitea.localhost.com
- **Harbor**: http://harbor.localhost.com
- **SonarQube**: http://sonarqube.localhost.com
- **ArgoCD**: http://argocd.localhost.com

### 🔑 Credenciais

Para obter as senhas dos serviços, use:

```bash
make passwd
```

Este comando irá exibir as credenciais de todos os serviços.

**Ou obtenha manualmente:**

**Jenkins:**
```bash
kubectl get secret -n jenkins jenkins -ojson | jq -r '.data."jenkins-admin-password"' | base64 -d
```

**ArgoCD:**
```bash
kubectl get secret -n argocd argocd-initial-admin-secret -ojson | jq -r '.data.password' | base64 -d
```

**Gitea, SonarQube e Harbor:**
- As credenciais padrão estão definidas nos arquivos `values/` de cada serviço
- Consulte o administrador do sistema ou verifique os values files

## 🔧 Comandos Úteis

### Gerenciar o Cluster

```bash
# Criar cluster
make create

# Instalar serviços
make helm

# Criar cluster + instalar tudo
make up

# Destruir cluster
make destroy
```

### Verificar Status dos Pods

```bash
kubectl get pods --all-namespaces
```

### Verificar Serviços

```bash
kubectl get svc --all-namespaces
```

### Logs de um Pod

```bash
kubectl logs -n <namespace> <pod-name>
```

## 📦 Pipeline Jenkins

O Jenkins está configurado com uma shared library em `jenkins-shared-libraries/` que contém:

- `pythonPipeline.groovy` - Pipeline para projetos Python
- `pythonUnitTest.groovy` - Testes unitários Python
- `kanikoBuildPush.groovy` - Build e push de imagens Docker
- `harborSecurityScan.groovy` - Scan de segurança no Harbor
- E outros...

### Exemplo de Jenkinsfile

```groovy
@Library('jenkins-shared-libraries')_

pythonPipeline {}
```

## 🐛 Troubleshooting

### Nginx Ingress não está funcionando

```bash
kubectl get pods -n ingress-nginx
kubectl logs -n ingress-nginx <ingress-controller-pod>
```

### Serviços não estão acessíveis

1. Verifique se o `/etc/hosts` está configurado corretamente:
```bash
grep "localhost.com" /etc/hosts
```

2. Verifique se o MetalLB está funcionando:
```bash
kubectl get svc -n ingress-nginx
```

3. Verifique se o Ingress Controller está rodando:
```bash
kubectl get pods -n ingress-nginx
```

### Reiniciar o Cluster

Se o cluster estiver com problemas, você pode recriá-lo:

```bash
make destroy
make up
```

**⚠️ ATENÇÃO:** Isso irá apagar todos os dados! Faça backup antes.

## 📁 Estrutura do Projeto

```
.
├── app/                          # Aplicação Python de exemplo
├── helm-applications/            # Charts Helm customizados
├── jenkins-shared-libraries/     # Shared libraries do Jenkins
├── manifests/                    # Manifestos Kubernetes
├── values/                       # Values para os charts Helm
│   ├── ingress-nginx/
│   ├── jenkins/
│   ├── gitea/
│   ├── harbor/
│   ├── sonarqube/
│   └── argocd/
├── config.yaml                   # Configuração do cluster Kind
├── helmfile.yaml                 # Definição dos releases Helm
├── Makefile                      # Comandos úteis
├── update-hosts.sh               # Script para configurar /etc/hosts
└── README.md                     # Este arquivo
```

## 🤝 Contribuindo

1. Faça um fork do projeto
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

## 📝 Licença

Este projeto está sob a licença MIT.

## 🆘 Suporte

Para problemas ou dúvidas, abra uma issue no repositório.
