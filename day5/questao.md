## DAY5

### Documentação do Dia

- Pagina ETCD: https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/

- Built-in Snapshot: https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#built-in-snapshot


### Questao 1
O nosso gerente solicitou que seja feito agora, um snapshot do nosso ETCD. Ele ficou assustado em saber que se perdermos o ETCD, perderemos o nosso cluster e, consequentemente, a nossa tranquilidade! Portanto, bora fazer esse snapshot imediatamente!

<details>
 <summary><b>  Pré-Tasks 1 </b> <em>(clique para ver as pré-tasks)</em></summary>

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
</details>
<details>
 <summary><b> Resposta 1 </b> <em>(clique para ver a resposta)</em></summary>

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
      - Dentro do pasta /etc/kubernetes/manifests/, execute para retornar as infos dos certificados: 
        ```markdown
        grep etcd kube-apiserver.yaml
        ```
        - Saída:
        ```bash
          --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
          --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
          --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
          --etcd-servers=https://127.0.0.1:2379 (Endpoint da DOC!!!!)
        ```
      - Executar o seguinte comando 
        ```markdown
        ETCDCTL_API=3 etcdctl snapshot save nome_snap_etcd.db --key /etc/kubernetes/pki/apiserver-etcd-client.key --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/apiserver-etcd-client.crt
        ```
        Enter na bagaça!!!
        - Saída:
        ```bash
        2021-10-17 23:49:24.222270 I | clientv3: opened snapshot stream; downloading
        2021-10-17 23:49:24.328184 I | clientv3: completed snapshot read; closing
        Snapshot saved at nome_snap_etcd.db
        ```
        Backup Feito!!!
</details>

### Questao 2
Muito bem, o gerente está feliz, mas não perfeitamente explendido em sua felicidade! A pergunta do gerente foi a seguinte, vc já fez o restire para testar o nosso snapshot? EU QUERO TESTAR AGORA!

<details>
 <summary><b>  Pré-Tasks 2 </b> <em>(clique para ver as pré-tasks)</em></summary>

- Criar um Pod 
```markdown
kubectl run strigus --image nginx
```

- Checar endereco/Path original do etcd. Abrir arquivo /etc/kubernetes/manifests/etcd.yaml e verificar linha --data-dir
```markdown
--data-dir=/var/lib/etcd
``` 

</details>
<details>
 <summary><b> Resposta 2 </b> <em>(clique para ver a resposta)</em></summary>

- Caso, queira testar, podemos jogar para um path qualquer e ajustar a conf do etcd.yaml para este path qualquer alterando o  :
```markdown
ETCDCTL_API=3 etcdctl snapshot restore snap_do_gerente.db -data-dir /tmp/etcd-test
```

- Aplicar comando de restore no path do ETCD
```markdown
ETCDCTL_API=3 etcdctl snapshot restore nome_snap_etcd.db
```

</details>