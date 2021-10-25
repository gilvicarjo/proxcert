## DAY6

### Documentação do Dia

- 

- 


### Questao 1
O nosso gerente observou no Lens que um dos nossos nodes não está bem. Temos algum problema com o nosso cluster e precisamos resolver agora.

<details>
 
 <summary><b>  Pré-Tasks 1 </b> <em>(clique para ver as pré-tasks)</em></summary>

- Com o comando abaixo, verificamos alguns CrashLoopBacks.
```
k get pods -A 
```
- Com o comando abaixo, verificamos um Node com status NotReady.
```
k get nodes
``` 
- Entramos nos node Not Ready
```
k describe nodes "nome_node"
```
- Podemos então entrar no Host via SSH e checar o kubelet 
```
systemctl status kubelet
```
![](images/status-kubelet.png)

</details>

<details>

 <summary><b> Resposta 1 </b> <em>(clique para ver a resposta)</em></summary>

</details>

### Questao 2

<details>
 <summary><b>  Pré-Tasks 2 </b> <em>(clique para ver as pré-tasks)</em></summary>


</details>
<details>
 <summary><b> Resposta 2 </b> <em>(clique para ver a resposta)</em></summary>

</details>