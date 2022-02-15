# k8s-help



# Cria o deployment pela linha de comando
kubectl create deployment nginx --image nginx --port=80

# faz o expose do deployment
kubectl expose deployment nginx

# Faz scale do replicaset pelo nome do deployment
kubectl scale --replicas=10 deployment nginx

# Deleta os pods que estão em Terminating eternamente
for p in $(kubectl get pods | grep Terminating | awk '{print $1}'); do kubectl delete pod $p --grace-period=0 --force;done

