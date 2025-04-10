# Hello World com Kubernetes Languages
![Toolbox Playground](img/toolbox-playground.png)

## O que é o Hello World com Kubernetes Languages

Um repositório com "Hello World" nas seguintes linguagens usando Kubernetes:

1. NodeJs
2. Python
3. .Net
4. Java
5. Go

## Uso

1. Certifique-se de ter instaldos:

a. Git: instalar Git a partir do [site oficial](https://git-scm.com/downloads).

b. Docker: a base para rodar contêineres. Você pode baixá-lo e instalá-lo a partir do [site oficial do Docker](https://docs.docker.com/get-docker/).

c. Kind (Kubernetes in Docker): permite executar clusters Kubernetes utilizando contêineres Docker. É ideal para testes rápidos e desenvolvimento local. Instale o Kind seguindo [estas instruções](https://kind.sigs.k8s.io/docs/user/quick-start/#installation).

d. kubectl: a linha de comando do Kubernetes para gerenciar clusters e contêineres. Instale o kubectl seguindo as instruções disponíveis na [documentação oficial do Kubernetes](https://kubernetes.io/releases/download/#kubectl).

2. Abra o terminal ou prompt de comando e navegue até um diretório de sua prefrência. Substitua `suapasta` pelo nome do seu diretório:
   ```bash
   cd suapasta
   ```

3.Clone este repositório para sua máquina local através do comando abaixo:
```bash
git clone https://github.com/toolbox-playground/hello-world-com-kubernetes-languages.git
```

4. Navegue até o diretório que você acabou de clonar.

   ```bash
    cd hello-world-com-kubernetes-languages
   ```

5. Escolha a linguagem de programação de sua prefrência disponível:

- NodeJs:

1. Navegue até o diretório NodeJs.
   ```bash
   cd nodejs
   ```
2. Siga as instruções do [NodeJs](./nodejs/README.md).

- Python:

1. Navegue até o diretório Python.
   ```bash
   cd python
   ```
2. Siga as instruções do [Python](./python/README.md)

- .Net:

1. Navegue até o diretório .Net.
   ```bash
   cd dotnet
   ```
2. Siga as instruções do [.Net](./dotnet/README.md)

- Java:

1. Navegue até o diretório Java.
   ```bash
   cd java
   ```
2. Siga as instruções do [Java](./java/README.md)

- Go:

1. Navegue até o diretório Go.
   ```bash
   cd go
   ```
2. Siga as instruções do [Go](./go/README.md)

## Comandos Kubernetes Úteis

Aqui está uma lista de comandos Kubernetes úteis para gerenciar seus recursos:

### Gerenciamento de Namespace
```bash
# Criar um namespace
kubectl create namespace <nome-do-namespace>

# Listar pods em um namespace específico
kubectl get pods --namespace=<nome-do-namespace>
```

### Gerenciamento de Pods e Deployments
```bash
# Criar um pod
kubectl run <nome-do-pod> --image=<nome-da-imagem> --namespace=<nome-do-namespace>

# Ver logs de um pod
kubectl logs <nome-do-pod> --namespace=<nome-do-namespace>

# Criar um deployment
kubectl create deployment <nome-do-deployment> --image=<nome-da-imagem> --namespace=<nome-do-namespace>

# Expor um deployment como serviço
kubectl expose deployment <nome-do-deployment> --type=NodePort --port=<porta> --namespace=<nome-do-namespace>

# Verificar serviço exposto
kubectl get service <nome-do-service> --namespace=<nome-do-namespace>

# Escalar um deployment
kubectl scale deployment <nome-do-deployment> --replicas=<número-de-réplicas> --namespace=<nome-do-namespace>

# Descrever um deployment
kubectl describe deployment <nome-do-deployment> --namespace=<nome-do-namespace>
```

### Limpeza de Recursos
```bash
# Deletar um pod
kubectl delete pod <nome-do-pod> --namespace=<nome-do-namespace>

# Deletar um deployment
kubectl delete deployment <nome-do-deployment> --namespace=<nome-do-namespace>

# Deletar um serviço
kubectl delete service <nome-do-service> --namespace=<nome-do-namespace>

# Deletar um namespace
kubectl delete namespace <nome-do-namespace>
```

### Gerenciamento de Cluster
```bash
# Verificar nós do cluster
kubectl get nodes

# Listar todos os clusters disponíveis
kubectl config get-contexts

# Mudar para um contexto de cluster específico
kubectl config use-context <nome-do-contexto>
```

### Docker (para Kind)
```bash
# Encontrar IP do node (útil quando usando Kind)
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -q --filter "name=kind-control-plane")
```

## Contribuindo

Contribuições são bem-vindas! Por favor, leia o arquivo [CONTRIBUTING.md](CONTRIBUTING.md) para mais detalhes.

## Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo [LICENSE.md](LICENSE.md) para mais detalhes.
