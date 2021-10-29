## DAY6

### Documentação do Dia

- 

- 


### Questao 1
O nosso gerente observou no Lens que um dos nossos nodes não está bem. Temos algum problema com o nosso cluster e precisamos resolver agora.

<details>
 
 <summary><b>  Pré-Tasks 1 </b> <em>(clique para ver as pré-tasks)</em></summary>

- Com o comando abaixo, verificamos alguns CrashLoopBacks.
```markdown
k get pods -A 
```
- Com o comando abaixo, verificamos um Node com status NotReady.
```markdown
k get nodes
``` 
- Entramos nos node Not Ready
```markdown
k describe nodes "nome_node"
```
- Podemos então entrar no Host via SSH e checar o kubelet 
```markdown
systemctl status kubelet
```
![](images/status-kubelet.png)

- Checamos então se o kubelet ao menos está rodando
```markdown
ps -ef | grep kubelet
```
![](images/status-kubelet-II.png)

- Checamos se existe o /usr/local/bin/kubelet
```markdown
ls -lha /usr/local/bin/kubelet
```

- Checamos agora o binário e manpage do kubelet
```markdown
whereis kubelet
```
![](images/whereis-binario-kubelet.png)

Com isto percebemos que o Systemctl está apontando para o path ERRADO do binario do Kubelet.

Se executarmos /usr/local/bin/kubelet não teremos retorno algum
Por outro lado, se executarmos /usr/bin/kubelet, neste sim teremos resposta do serviço.

Desta forma, só nos resta ajustar a path do binario do kubelet no arquivo de configuracao do Systemd.

</details>

<details>

 <summary><b> Resposta 1 </b> <em>(clique para ver a resposta)</em></summary>


Entramos no arquivo de configuracao do Systemd relativo ao kubelet e identificamos o path errado.
```markdown
vim /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```
![](images/systemd-kubelet-config.png)


Alteramos para o path correto.

![](images/systemd-kubelet-fixed.png)


Reload no Systemd e Kubelet, para re-ler todos os arquivos de configuracao.
```markdown
systemctl daemon-reload
```
```markdown
systemctl restart kubelet
```
```markdown
systemctl status kubelet
```

![](images/kubelet-running.png)

```markdown
ps -ef | grep kubelet
```
![](images/ps_-ef_|_grep_kubelet.png)

```markdown
journalctl -u kubelet
```

Verificamos o status dos PODs e Nodes do Cluster novamente

```markdown
k get nodes
``` 

```markdown
k get pods -A 
```

</details>

### Questao 2
Temos um secret com o nome e senha de um usuario que nossa aplicacao irá utilizar, precisamos colocar esse secret em um pod.
**Detalhe, esse secret deve se tornar uma variavel de ambiente dentro do container.**
No exame:
- Nome do Pod
- Nome do Secret



<details>
 <summary><b>  Pré-Tasks 2 </b> <em>(clique para ver as pré-tasks)</em></summary>

Cria secret
```markdown
❯ k create secret 
```
Help do Secret
```markdown
❯ k create secret --help
```
```r
Create a secret using specified subcommand.

Available Commands:
  docker-registry Create a secret for use with a Docker registry
  generic         Create a secret from a local file, directory, or literal value
  tls             Create a TLS secret
```
Vamos usar o _generic_
```markdown
❯ k create secret generic --help
```
```markdown
❯ k create secret generic
```


</details>
<details>
 <summary><b> Resposta 2 </b> <em>(clique para ver a resposta)</em></summary>

Exemplo de execucao (apenas dry-run):
```markdown
❯ k create secret generic credentials --from-literal user=gil --from-literal password=d@quelej3ito --dry-run=client -o yaml
```
```yaml
apiVersion: v1
data:
  password: ZEBxdWVsZWozaXRv
  user: Z2ls
kind: Secret
metadata:
  creationTimestamp: null
  name: credentials
```
Verificando o que aplicamos
```markdown
k get secrets -o wide
```
```                                                                                           
NAME                  TYPE                                  DATA   AGE
credentials           Opaque                                2      3s
```
Vamos subir um NGINX maroto e jogar nosso secret nele! primeiramente criamos o YAML!
```markdown
❯ k run gilzera --image nginx --dry-run=client -o yaml > day6/pod-secret.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: gilzera
  name: gilzera
spec:
  containers:
  - image: nginx
    name: gilzera
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
Após editar o nosso manifesto do NGINX com a nossa secret dentro do arquivo pod-secret.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: gilzera
  name: gilzera
spec:
  containers:
  - image: nginx
    name: gilzera
    resources: {}
    env: 
    - name: MEU_USER
      valueFrom:
        secretKeyRef: 
          name: credentials #nome-da-secret
          key: user
    - name: MEU_PASSWORD
      valueFrom:
        secretKeyRef: 
          name: credentials #nome-da-secret
          key: password
    volumeMounts:
    - name: credentials #nome-da-secret
      mountPath: /opt/gilzera
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
  - name: credentials
    secret:
      secretName: credentials
```
Executamos a criaçao efetiva deste POD
```markdown
k create -f pod-secret.yaml
```
Na sequencia acompanhamos a inicializacao do POD
```markdown
k get pods -o wide -A
```
Checamos mais detalhadamente com k describe
```markdown
k describe pod gilzera -n default
```
Agora vamos entrar no POD que acabamos de criar
```markdown
k exec -ti gilzera -- bash
```
E dentro dele checar as variaveis de ambiente
```r
❯ k exec -ti gilzera -- bash       
root@gilzera:/# env
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_PORT=443
MEU_USER=gil
HOSTNAME=gilzera
PWD=/
PKG_RELEASE=1~buster
HOME=/root
KUBERNETES_PORT_443_TCP=tcp://10.0.0.1:443
NJS_VERSION=0.6.2
TERM=xterm
SHLVL=1
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_ADDR=10.0.0.1
MEU_PASSWORD=d@quelej3ito
KUBERNETES_SERVICE_HOST=10.0.0.1
KUBERNETES_PORT=tcp://10.0.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NGINX_VERSION=1.21.3
_=/usr/bin/env
```
Vamos agora conferir o volume
```r
root@gilzera:/# cat opt/gilzera/
..2021_10_28_23_35_33.206026874/ 
..data/                          
password                         
user                             
root@gilzera:/# cat opt/gilzera/user
gil
root@gilzera:/# cat opt/gilzera/password
d@quelej3ito
```

</details>