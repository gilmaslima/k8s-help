# k8s-help



# Cria o deployment pela linha de comando
kubectl create deployment nginx --image nginx --port=80

# faz o expose do deployment
kubectl expose deployment nginx

# Faz scale do replicaset pelo nome do deployment
kubectl scale --replicas=10 deployment nginx

# Deleta os pods que estão em Terminating eternamente
for p in $(kubectl get pods | grep Terminating | awk '{print $1}'); do kubectl delete pod $p --grace-period=0 --force;done

# Ver os logs do pod (parâmetro pod name)
kubectl logs -f nginx-74d589986c-25mww 

# Documentação dos recursos 
kubectl explain deployments.spec.revisionHistoryLimit

### Ao criar um Service é criado um ClusterIp se o type não for definido
### Ao criar um Service to tipo NodePort também é criado um ClusterIp
### Ao criar um Service to tipo LoadBalancer também são criados um NodePort e um ClusterIp
### O LoadBalancer rodando local não cria um EXTERNAL IP porque isso depende do Cloud provider
