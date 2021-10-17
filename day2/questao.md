Obs: No início da prova não esquecer de
* habilitar o atalho: alias k=kubectl
* habilitar o kubectl completion
* a prova é disponibilizada em um ambiente na hora tudo pelo navegador

Livro: https://livro.descomplicandokubernetes.com.br/pt/day_one/descomplicando_kubernetes.html

--------------------------------------------------
# Questão 1

Precisamos levantar algumas informações sobre o nosso cluster:
- Quantos nodes são workers?
- Quantos nodes são masters?
- Qual o Pod Network (CNI) que estamos utilizando?
- Qual o CIDR dos pods no segundo Worker?
- Qual o DNS que estamos utilizando para o Cluster?

Adicionar as informações colidas no arquivo ~/cluster_info.txt

> Notes: CNI - Forma de ter comunicacao de PODs entre Nodes diferentes. Ex: Calico, Flannel, WeaveNet e etc. Deve rodar dentro do kube-system

### Pré-Tasks

- Criar Cluster 3 Masters e 3 Workers

Rodar o comando que cria o Namespace **strigus**
```
kubectl get nodes
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


