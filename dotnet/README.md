# Hello World .Net com K8s
![Toolbox](../img/toolbox-playground.png)

## Uso Local

Para usar o projeto Hello World .Net com K8s, siga estes passos:


1. Certifique-se que você está dentro do diretório `k8s-exemplos-deployments/docker-images/dotnet`.

2. Construa a imagem Docker:
```bash
docker build -t hello-world-dotnet:latest
```
Obs.: Certifique-se que seu Docker está rodando.

3. Taggear a imagem para garantir que ela corresponda ao nome que será usado no Kubernetes:
```bash
docker tag hello-world-dotnet:latest localhost/hello-world-dotnet:latest
```

4. Por último, verifique se a imagem foi criada corretamente:
```bash
docker images localhost/hello-world-dotnet
```
![docker images](./img/docker-images.png)

## Deploy com Kubernetes

Para fazer o deploy no cluster K8s, primeiro vamos criar e configurar o cluster:

1. Certifique-se de que o Kubernetes e Kind estão rodando.
```bash
kubectl version
kind version
```

1.1. Se nenhum dos comandos abaixo funcionar é porque ou não estão instalados ou não foram inicializados corretamente -> Neste caso volte à seção de configuração local e revise a instalação das 3 ferramentas.

2. Verifique os clusters disponíveis. Se houver um cluster default, você pode usá-lo. Se não, crie um novo cluster e use-o.
```bash
# Listar todos os clusters disponíveis
kubectl config get-contexts

# Se não existir nenhum cluster ativo, ou se você preferir criar um novo cluster com Kind
kind create cluster --name <nome-do--novo-cluster>

# Mudar para um contexto de cluster específico
kubectl config use-context <nome-do-cluster>
```

3. [Opcional] Você pode usar o namespace default existente ou criar um novo
```bash
# Listar os namespaces existentes
kubectl get namespaces

# Configurar um dos namespaces como padrão
kubectl config set-context --current --namespace=<namespace-name>

# Se desejar criar e usar um namespace novo chamado 'my-namespace' ao invés de usar um namespace default existente
kubectl create namespace my-namespace
kubectl config set-context --current --namespace=my-namespace
```

Agora que temos um cluster e namespace funcionando e configurado, vamos criar o manifesto Kubernetes para fazer o deploy desta aplicação. Crie um arquivo chamado [manifest.yaml](manifest.yaml) no diretório da aplicação (e.g. `touch manifest.yaml` dentro do diretório `hello-world-com-docker-languages/dotnet/`) e adicione o seguinte conteúdo neste novo arquivo:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: dotnet-hello-world
spec:
 replicas: 1
 selector:
   matchLabels:
     app: dotnet-hello-world
 template:
   metadata:
     labels:
       app: dotnet-hello-world
   spec:
     containers:
     - name: dotnet-hello-world
       image: localhost/hello-world-dotnet:latest
       imagePullPolicy: IfNotPresent
       ports:
       - containerPort: 8080
       resources:
         requests:
           cpu: "250m"
           memory: "256Mi"
         limits:
           cpu: "500m"
           memory: "512Mi"

---
apiVersion: v1
kind: Service
metadata:
 name: dotnet-hello-world-service
spec:
 type: NodePort
 ports:
 - port: 8080
   nodePort: 30000   #Altere a porta do node o quanto você quiser em um range de 30000-32767
 selector:
   app: dotnet-hello-world
```

Este arquivo define um Deployment e um Service para a aplicação. O Deployment específica que queremos uma réplica da aplicação rodando, e o Service expõe a aplicação na porta 30000 do nó.

Para aplicar este manifesto e fazer o deploy da aplicação no seu cluster Kubernetes, use o comando:
```bash
kubectl apply -f manifest.yaml
```

No fim disso, você terá uma aplicação rodando em cima de um container, como mostra a imagem a seguir. Além disso, você poderá acessar a aplicação através de http://localhost:30000 após o deploy.

![docker images](./img/docker-ps.png)

[Opcional]  A criação do Deployment e do Serviço também podem ser feito via linha de comando e sem a necessidade da criação do manifesto, mas encorajamos que criem o manifesto por ser uma boa prática.

```bash
# Exemplo Criação de Deployment e Service via kubectl command line e sem o manifesto

# Criar um deployment chamado 'my-deployment' com a imagem do Grafana
kubectl create deployment my-deployment --image=localhost/hello-world-dotnet:latest --namespace=my-namespace
# Verificar o Deployment
kubectl get deployments --namespace=my-namespace

# Expor o deployment 'my-deployment' via NodePort
kubectl expose deployment caio-deployment --type=LoadBalancer --port=80 --target-port=3000 --protocol=TCP

# Obter o serviço exposto para encontrar o NodePort atribuído
kubectl get service my-deployment --namespace=my-namespace
```

## Explicando o manifesto k8s

O manifesto do Kubernetes define a configuração e o comportamento dos recursos no cluster. As partes essenciais do manifesto incluem o tipo de recurso (kind), especificações (spec), e configurações dos contêineres (containers) com detalhes sobre imagens, portas, e recursos de computação.

### Diferença entre kubectl apply e kubectl create:

**kubectl apply**: Recomendado para gerenciamento declarativo dos recursos, ou seja quando declaramos um arquivo yaml contendo os recursos do serviço e das pods. Permite atualizar e criar recursos com base no arquivo de manifesto. Mantém um registro dos arquivos aplicados para permitir atualizações contínuas.

**kubectl create**: Usado para criação imperativa. Cria recursos baseando-se em um arquivo ou stdin (linha de comando, como fizemos no exemplo anterior), mas não gerencia mudanças incrementais ou registros.

Usar kubectl apply oferece mais flexibilidade para atualizações contínuas e gerenciamento de estado desejado, enquanto kubectl create é útil para criações simples e diretas sem a necessidade de atualizações futuras.

A seguir, alguns objetos chave do arquivo manifesto:

1. **kind: Deployment**
O Deployment controla a criação, atualização e remoção de pods de acordo com as definições especificadas. Ele ajuda a manter as instâncias de aplicativos atualizadas.

2. **spec**:
Define o comportamento desejado do pod, detalhando como os contêineres devem operar.

3. **replicas**:
Especifica o número de réplicas/pods que o Kubernetes deve manter em execução. O padrão é 1 se não especificado.

4. **containers**:
Lista de contêineres dentro do pod. A configuração de cada contêiner, incluindo a imagem a ser usada, portas a serem expostas e os recursos necessários.

5. **resources**:
- Define os limites de recursos para os contêineres, como CPU e memória, garantindo que o contêiner não ultrapasse os recursos alocados.

- **requests**: Especifica a quantidade mínima de recursos que o contêiner deve ter garantido.

- **limits**: Define o máximo de recursos que o contêiner pode usar.

Através desse arquivo Deployment, o Kubernetes pode gerenciar automaticamente a aplicação, escalonando-a ou ajustando-a conforme necessário baseado nas definições de recursos e réplicas. Para mais informações, consulte a documentação oficial.

## Testando a App Rodando Após Deploy no Cluster K8S

Com todos os passos anteriores feitos, sua aplicação deve estar rodando localmente sobre um cluster kubernetes. Você pode executar os seguintes comandos para verificar seu deployment, serviço e pods rodando para o seu service. Esta seção vai mostrar como operar seu serviço através de **Comandos Kubernetes** para:

1. Verificar Deployment, Serviços e pods: 
```bash
kubectl get deployment
kubectl describe deployment dotnet-hello-world (opcional)
kubectl get service
kubectl get pods ou docker ps
kubectl describe pod <nome-do-pod>
```

![docker images](./img/kubectl.png)

2. **Acesso à aplicação**: A aplicação pode ser acessada através do endereço http://localhost:30000 após o deploy, dependendo da configuração do NodePort no serviço, e verá que sua aplicação dotnet está Up & Running:

![docker images](./img/app.png)

3. **Pods: Logs, Debug, Acesso e Troubleshooting**:
```bash
kubectl get pods
kubectl logs <my_pod_name>

# Executar um shell interativo em um contêiner específico
kubectl exec -it <nome-do-pod> -- /bin/bash

# Iniciar uma sessão de debug para um pod problemático
kubectl debug -it <nome-do-pod> --image=<imagem-de-debug> --copy-to=<nome-do-pod-debug>
```

![docker images](./img/pods.png)

4. **Visualizar uso de recursos**: Deve ter o [MetricsServer](https://github.com/kubernetes-sigs/metrics-server.git) habilitado.
```bash
# Ver uso de CPU e memória dos pods
kubectl top pod

# Ver uso de CPU e memória dos nós
kubectl top nodes
```

5. **Listar eventos do cluster**:
```bash
# Listar eventos recentes
kubectl get events
```

![docker images](./img/events.png)

6. [Opcional] Se você tiver o kubernetes lens ou docker desktop instalados localmente, explore também os recursos utilizando a UI que essas ferramentas fornecem, como mostramos a seguir:

![docker images](./img/docker-desktop.png)