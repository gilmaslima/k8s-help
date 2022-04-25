# k8s-help



## Cria o deployment pela linha de comando
```
kubectl create deployment nginx --image nginx --port=80
```

## faz o expose do deployment
```
kubectl expose deployment nginx
```

## Faz scale do replicaset pelo nome do deployment
```
kubectl scale --replicas=10 deployment nginx
```

## Deleta os pods que estão em Terminating eternamente
```
for p in $(kubectl get pods | grep Terminating | awk '{print $1}'); do kubectl delete pod $p --grace-period=0 --force;done
```

## Ver os logs do pod (parâmetro pod name)
```
kubectl logs -f nginx-74d589986c-25mww 
```

## Documentação dos recursos 
```
kubectl explain deployments.spec.revisionHistoryLimit
```

### Ao criar um Service é criado um ClusterIp se o type não for definido
### Ao criar um Service to tipo NodePort também é criado um ClusterIp
### Ao criar um Service to tipo LoadBalancer também são criados um NodePort e um ClusterIp
### O LoadBalancer rodando local não cria um EXTERNAL IP porque isso depende do Cloud provider

## Conectando no shell do pod
```
kubectl exec -ti nginx-fbdcfcfc5-5dhxr -- bash
```

## Filtrando os pods por label
```
kubectl get pods -l dc=UK
```

## Adicionando o campo label no output
```
kubectl get pod -L dc
```

## Adicionando label ao node 
```
kubectl label nodes k8-node0 disk=HDD
```

## Sobreescrevendo um label no node
```
kubectl label nodes k8-node0 disk=SSD --overwrite
```

## Listando os labels de um node
```
kubectl label nodes k8-node1 --list
```

## Deletando um label de um ou mais nodes. (disk é o nome do label)
```
kubectl label nodes --all disk- 
```

## Deletando um label de um node específico. (disk é o nome do label)
```
kubectl label nodes k8s-master disk-
```

## Comandos - Rollouts e Rollbacks
```
kubectl rollout history ds daemon-set-primeiro
kubectl rollout history ds daemon-set-primeiro --revision=1
kubectl rollout history ds daemon-set-primeiro --revision=2

kubectl rollout undo ds daemon-set-primeiro --to-revision=1

kubectl rollout status ds daemon-set-primeiro 

kubectl describe daemon-set-primeiro-hp4qc | grep -i image:

kubectl delete -f primeiro-daemonset.yaml
```



