Obs: No início da prova não esquecer de
* habilitar o atalho: alias k=kubectl
* habilitar o kubectl completion
* a prova é disponibilizada em um ambiente na hora tudo pelo navegador

Livro: https://livro.descomplicandokubernetes.com.br/pt/day_one/descomplicando_kubernetes.html

--------------------------------------------------
# Questão 1

Criar um pod estático utilizando a imagem do nginx

Nesse dia aprendi sobre: **--pod-manifest-path**, na documentacao do Pod Static: https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/

### Pré-Tasks

Criar Cluster Kubernetes no Minikube

```markdown
minikube start --nodes 2 -p multinode-demo --cpus 2 --memory 2048 --driver=hyperkit
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
Outro exemplo muito ÚTIL para o uso do "**--dry-run**"

![](/images/exemplo-pod-nginx-kubectl-run.png)

### Sobre conectar no minikube via SSH

Checar configuracoes do minikube em: 
```
$ cat .minikube/machines/minikube/config.json
```
```bash
$ scp -i $PATH/.minikube/machines/minikube/id_rsa -r <file> docker@<ip>:~
$ ssh -i $PATH/.minikube/machines/minikube/id_rsa <user>@<ip>
```
![](/images/ssh-minikube.png)

## Resposta 1

### Forma 1: Via CLI

### Forma 2: Via Manifesto (Mais Recomendada) 

--------------------------------------------------

# Questão 2
- 

### Pré-Tasks


## Resposta 2

### Forma 1: Via CLI


### Forma 2: Via Manifesto


### Forna3: Via kubectl edit

--------------------------------------------------

# Questão 3

- 

### Pré-Tasks



## Resposta 3

### Forma 1: Via CLI (kubectl set)



### Forma 2: Via Manifesto



### Forna3: Via kubectl edit


