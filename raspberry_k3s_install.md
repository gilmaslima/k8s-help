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
curl https://releases.rancher.com/install-docker/20.10.sh | sh
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

## Instalar versão específica

```
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.22.6+k3s1 INSTALL_K3S_SKIP_ENABLE=true sh -
```

## pegar o token no master e executar o comando abaixo para instalar o k3s no worker e adcionalo ao cluster como worker node
```
nohup k3s agent --token K106aec0d246b8a2b5bd08ad4a4249aa1707de1bc1268da8a18de9f6c2b21530bfc::server:5443b13f487f80a4879bff5e71312c01 --server https://192.168.0.22:6443 --docker &>/dev/null &
```

## caso seja necessario desinstalar
```
/usr/local/bin/k3s-uninstall.sh
```

## repositório de versões do k3s
```
https://github.com/k3s-io/k3s/releases/tag/v1.22.6%2Bk3s1
```

## habilitar o iptables legacy no k3s em caso de falha ao adicionar um novo worker node
```
update-alternatives --set iptables /usr/sbin/iptables-legacy
update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```


###############################
Local volume

K3s Static folder for volume
https://blog.differentpla.net/blog/2021/12/17/k3s-static-pv/

static local Volume work around
https://mrkandreev.name/snippets/how_to_move_k3s_data_to_another_location/#:~:text=The%20standard%20data%20location%20used,can%20use%20separate%20data%20volume.

##################$$$$$$$$

USB as volume
https://forum.endeavouros.com/t/tutorial-how-to-permanently-mount-external-internal-drives-in-linux/18688
