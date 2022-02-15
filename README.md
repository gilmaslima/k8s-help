# k8s-help



# Cria o deployment pela linha de comando
kubectl create deployment nginx --image nginx --port=80

# faz o expose do deployment
kubectl expose deployment nginx

# Faz scale do replicaset pelo nome do deployment
kubectl scale --replicas=10 deployment nginx
