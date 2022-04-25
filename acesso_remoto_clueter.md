# Para acessar o Cluster k3s remotamente seguir os passos abaixo:

No Master copiar a saída do comando

```
cat /etc/rancher/k3s/k3s.yaml
```

<br>
Copiar a saída do comando para o arquivo de configuração local do k8s

```
~/.kube/config
```

Obs: Trocar o server para o ip/dns do servidor
