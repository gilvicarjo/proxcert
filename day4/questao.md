## DAY4

### Questao 1
Precisamos subir um container em um node master, esse container tem que estar rodando a imagem do NGINX, o nome do pod é POD-WEB e o container é CONTAINER-WEB. Sua namespace será CATOTA.

### Resposta 1

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod-web
  name: pod-web
  namespace: catota
spec:
  containers:
  - image: nginx
    name: pod-web
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  tolerations:
  - key: "node-role.kubernetes.io/master"
    effect: "NoSchedule"
    operator: Equal
  nodeSelector:
    node-role.kubernetes.io/master: ""
```
```markdown
kubectl create namespace catota
kubectl create -f pod.yaml
```

### Questao 2
Precisamos de algumas informacoes do nosso cluster e dos pods que lá estão. Portanto, precisamos do seguinte:
- Adicione todos os pods do cluster por ordem de criação, dentro do arquivo pods.txt
```markdown
k get pods --sort-by metadata.creationTimestamp -A -o name >> pods.txt
``

- Remova o pod do weave e verifique os eventos e os adicione no arquivo eventos.txt
k delete pod  nginx-deployment-66b6c48dd5-2ggzd


- Liste todos os pods que estão em execuçao no kind-worker e os adicione no arquivo kind-worker.txt

### Resposta 2

```markdown
k get pods --sort-by metadata.creationTimestamp -A -o name >> pods.txt
```

```markdown
k get events --all-namespaces --sort-by=metadata.creationTimestamp | grep nginx-deployment > eventos.txt
```

```markdown
k get pods --all-namespaces --field-selector spec.nodeName=kind-worker --no-headers -o name > kind-worker
```
