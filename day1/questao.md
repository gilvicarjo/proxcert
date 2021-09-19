Obs: No início da prova não esquecer de
* habilitar o atalho: alias k=kubectl
* habilitar o kubectl completion
* a prova é disponibilizada em um ambiente na hora tudo pelo navegador

# Questão 1

- Criar um pod utilizando a imagem do Nginx 1.18.0, com o nome de **giropops** no namespace **strigus**.

### Pré-Tasks

Rodar o comando que cria o Namespace **strigus**
```
kubectl create ns strigus
```

### Sobre o dry-run

Por exemplo, se pedirmos para listar um pod qualquer:
```
kubectl get pods -n kube-system storage-provisioner
```
Teremos uma saída simples:
```
NAME                  READY   STATUS    RESTARTS      AGE
storage-provisioner   1/1     Running   2 (36h ago)   2d4h
```
Mas se incluirmos o **-o yaml** ou **-o json** no final do comando, temos em como retorno uma saída mais detalhada com tudo que precisamos saber sobre o pod
```
kubectl get pods -n kube-system storage-provisioner -o yaml
ou
kubectl get pods -n kube-system storage-provisioner -o json
```

## Resposta 1

### Forma 1: Via CLI

```bash
kubectl run giropops --image nginx:1.18.0 --port 80 --namespace strigus --dry-run=client -o yaml

ou 

kubectl run giropops --image nginx:1.18.0 --port 80 --namespace strigus --dry-run=client -o yaml > pod-ns.yaml
```

Analisamos como o pod está configurado. <br> 
Retiramos parametros como:
* creationTimestamp: null (Pois está vazio!)
* status: {} (Pois está vazio!)
* resources: {} (Pois está vazio!)
  <br>

Na sequência, comando final:
```bash
kubectl run giropops --image nginx:1.18.0 --port 80 --namespace strigus
```

### Forma 2: Via Manifesto (Mais Recomendada) 

No arquivo **pod2.yaml** inserimos o manifesto padrao de um pod estático, que pode ser encontrado facilmente na documentacao do kubernetes, em: https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/ e alteramos os parametros (nome do pod, nome do namespace e imagem) de acordo com o que pede a questão. <br>
Com o manifesto **pod2.yaml** escrito, para atender a questão basta executar-mos:

```bash
kubectl create ns strigus
kubectl create -f pod.yaml
ou
kubectl create -f pod.yaml -n strigus
```
Obs: Mesmo com execucao do comando **create**, o Namespace deve obrigatoriamente ser criado

# Questão 2
- Aumentar a quantidade de réplicas do deployment **girus** no namespace **strigus**, que está utilizando a imagem do nginx 1.18.0, para 3 réplicas. 

### Pré-Tasks

```
kubectl create deployment girus --image nginx:1.18.0 --port 80 --namespace strigus --dry-run=client -o yaml > deployment1.yaml
kubectl create -f deployment1.yaml
```

## Resposta 2

### Forma 1: Via CLI

```
kubectl scale deployment -n strigus girus --replicas 3
```

### Forma 2: Via Manifesto

Executamos a criacao do deployment **em modo dry-run** considerando as 3 réplicas

```
kubectl create deployment girus --image nginx:1.18.0 --port 80 --namespace strigus --replicas 3 --dry-run=client -o yaml > deployment2.yaml
kubectl apply -f deployment2.yaml
```

### Forna3: Via kubectl edit

Podemos alterar qualquer configuração corrente com o kubectl edit da seguinte forma usando o exemplo da questao 2.

```
kubectl edit deploy -n strigus girus
```

# Questão 3
- Precisamos atualizar a versão do Nginx do Pod giropops. Ele está na versão 1.18.0 e precisamos atualizar para a versão 1.21.1 

### Pré-Tasks

```

```

## Resposta 3

### Forma 1: Via CLI (kubectl set)

```
k set image pod giropops -n strigus web=nginx:1.21.1 
```
Obs: "web" é o nome do container! Conseguimos validar isso rodando um **describe** ("k describe pods -n strigus giropops") no Pod giropops.
```
Containers:
  web:
    Container ID:   docker://fb25641070f9c5477097c8e99e7a693d1aedf3f13d4116011774d4fdb97665fd
```

Para validar a alteração basta rodar um kubectl describe no nosso pod.
```
k describe pods -n strigus giropops 
```
Teremos um retorno como este:
```
Events:
  Type    Reason   Age                 From     Message
  ----    ------   ----                ----     -------
  Normal  Pulling  29m                 kubelet  Pulling image "nginx:1.21.1"
  Normal  Pulled   29m                 kubelet  Successfully pulled image "nginx:1.21.1" in 10.230890583s
  Normal  Pulled   13m                 kubelet  Container image "nginx:1.18.0" already present on machine
  Normal  Killing  52s (x3 over 29m)   kubelet  Container web definition changed, will be restarted
  Normal  Created  51s (x4 over 120m)  kubelet  Created container web
  Normal  Started  51s (x4 over 120m)  kubelet  Started container web
  Normal  Pulled   51s                 kubelet  Container image "nginx:1.21.1" already present on machine
```

### Forma 2: Via Manifesto

Primeiro passo a se fazer é verificar a versão do Nginx no Pod

```
k describe pods -n strigus giropops | grep Image
```
Teremos um retorno como este:
```
Image:          nginx:1.18.0
Image ID:       docker-pullable://nginx@sha256:e90ac5331fe095cea01b121a3627174b2e33e06e83720e9a934c7b8ccc9c55a0
```

Em seguida podemos gerar um manifesto com:
```
k get pods -n strigus giropops -o yaml > pod4.yaml
```
Analisamos como o pod está configurado. <br> 
Retiramos parametros como:
* creationTimestamp: null (Pois está vazio!)
* managedFields
* resourceVersion
* uid
* status: {} (Pois está vazio!)
* resources: {} (Pois está vazio!)
  <br>

### Forna3: Via kubectl edit

Após executar o kubectl edit
```
k edit pods -n strigus giropops
```
Basta editar a imagem para a versão que a questão pede. <br>
Para validar a alteração basta rodar um kubectl describe no nosso pod.
```
k describe pods -n strigus giropops 
```
Teremos um retorno como este:
```
Events:
  Type    Reason   Age                 From     Message
  ----    ------   ----                ----     -------
  Normal  Killing  2m3s                kubelet  Container web definition changed, will be restarted
  Normal  Pulling  2m3s                kubelet  Pulling image "nginx:1.21.1"
  Normal  Created  112s (x2 over 92m)  kubelet  Created container web
  Normal  Started  112s (x2 over 92m)  kubelet  Started container web
  Normal  Pulled   112s                kubelet  Successfully pulled image "nginx:1.21.1" in 10.230890583s
```