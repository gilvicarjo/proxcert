## DAY5

### Documentação do Dia

- Pagina ETCD: https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/

- Built-in Snapshot: https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#built-in-snapshot


### Questao 1
O nosso gerente solicitou que seja feito agora, um backup/snapshot do nosso ETCD. Ele ficou assustado em saber que se perdermos o ETCD, perderemos o nosso cluster e, consequentemente, a nossa tranquilidade! Portanto, bora fazer esse snapshot imediatamente!

### Pré-Tasks 1

#### Instalar o ETCDCTL
- Ubuntu: 
```markdown
$ sudo apt-cache search etcd
$ sudo apt-get install etcd-client
```
- MacOS: 
```markdown
$ brew install etcd
```

#### Adicional - Instalar Weave
```markdown
$ kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
````
#### - Ver como configura trafego para endereco/porta externo do K8s


#### - Acessar Node do Cluster Kind
```markdown
$ docker exec -it <node> /bin/bash
```

#### - Acessar YAMLs dos servicos estaticos do Control Plane
```markdown
# cd /etc/kubernetes/manifests
etcd.yaml  
kube-apiserver.yaml  
kube-controller-manager.yaml  
kube-scheduler.yaml
```

### Resposta 1

1. Checar certificados do ETCD:
   - Dentro do Control-Plane 
```markdown
cat /etc/kubernetes/manifests/etcd.yaml
```

2. Seguir instruções da doc: 
   - Built-in Snapshot: https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#built-in-snapshot

      - Criar variável ETCDCTL_API e instalar o etcdctl no Node Control Plane, conforme abaixo:
        ```markdown
        # ETCDCTL_API=3
        # apt install etcd-client
        ```
      - Executar o seguinte comando 
        ```markdown
        # ETCDCTL_API=3 etcdctl snapshot save nome_backup_etcd.db
        ```
        Enter na bagaça!!!
### Questao 2

- 

### Resposta 2
