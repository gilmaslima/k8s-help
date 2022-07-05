# k3s raspberry pi

## atualizar o sistema
```
apt update && apt upgrade -y
```

## habilitar o cgroup
```
nano /boot/cmdline.txt
```

## incluir no começo do arquivo os parametros abaixo
```
cgroup_enable=memory cgroup_memory=1
```

## desabilitar o swap
```
sudo nano /etc/dphys-swapfile
```
## change CONF_SWAPSIZE=0
 
## reiniciar o raspberry
```
reboot
```

## instalar o docker do repositório do rancher
```
curl https://releases.rancher.com/install-docker/19.03.sh | sh
```

## Caso o release do rancher não estiver disponível procurar no repositório github deles
```
https://github.com/rancher/install-docker
```


## verificar se o cgroup esta configurado
```
docker info | grep -i cgroup
```
## Se a saída foi Cgroup Driver: systemd, tudo certo!


---------------------------------------------------------------
# Os comandos abaixo devem ser executado apenas no master     

## instala o k3s no master
```
curl -sfL https://get.k3s.io | sh -s - --docker 
```

## pegar o token para ser usado nos nodes/workers
```
cat /var/lib/rancher/k3s/server/node-token
```

---------------------------------------------------------------
# Troubleshooting
## Caso o certificado do master tenha expirado vai exibir o erro abaixo
## ERRO[0004] Failed to connect to proxy     error="x509: certificate is valid for 10.43.0.1, 127.0.0.1, 192.168.0.22, not 2804:14c:85:80f2:b870:7f42:f4c1:b233"

## Passo 1 -Verificar a expiração do certificado
```
openssl s_client -connect localhost:6443 -showcerts < /dev/null 2>&1 | openssl x509 -noout -enddate
```

## Fazer backup do diretório TLS
```
tar -czvf /var/lib/rancher/k3s/server/apphost-cert.tar.gz /var/lib/rancher/k3s/server/tls
```

## Excluir o arquivo abaixo
```
rm /var/lib/rancher/k3s/server/tls/dynamic-cert.json
```
## Remover arquivo de cache
```
kubectl --insecure-skip-tls-verify=true delete secret -n kube-system k3s-serving
```

## Reiniciar o k3s
```
systemctl restart k3s
```

## Verificando o status do cluster
```
systemctl status k3s
```

## Executar comando do Passo 1 para verificar a data de expiração


---------------------------------------------------------------

---------------------------------------------------------------
# O comando abaixo deve ser executado apenas nos worker nodes

## Instalar o k3s
```
curl https://releases.rancher.com/install-docker/19.03.sh | sh
```

## pegar o token no master e executar o comando abaixo para instalar o k3s no worker e adcionalo ao cluster como worker node
```
k3s agent --token K1071c516b7bafff0b300f4b51881c901fe777e60710c7c1cd41b07647aaec519ae::server:d0b7c93b0b0d616b39ca5f849298d508 --server https://192.168.0.22:6443 --docker
```

