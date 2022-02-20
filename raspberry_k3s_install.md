# k3s raspberry pi

## atualizar o sistema
apt update && apt upgrade -y

## habilitar o cgroup
nano /boot/cmdline.txt

## incluir no começo do arquivo os parametros abaixo
cgroup_enable=memory cgroup_memory=1

## desabilitar o swap
sudo nano /etc/dphys-swapfile
## change CONF_SWAPSIZE=0
 
## reiniciar o raspberry
reboot

## instalar o docker do repositório do rancher
curl https://releases.rancher.com/install-docker/19.03.sh | sh

## verificar se o cgroup esta configurado
docker info | grep -i cgroup
## Se a saída foi Cgroup Driver: systemd, tudo certo!


---------------------------------------------------------------
# Os comandos abaixo devem ser executado apenas no master     

## instala o k3s no master
curl -sfL https://get.k3s.io | sh -s - --docker 

## pegar o token para ser usado nos nodes/workers
cat /var/lib/rancher/k3s/server/node-token

---------------------------------------------------------------

---------------------------------------------------------------
# O comando abaixo deve ser executado apenas nos worker nodes

## pegar o token no master e executar o comando abaixo para instalar o k3s no worker e adcionalo ao cluster como worker node
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.0.22:6443 K3S_TOKEN=K1071c516b7bafff0b300f4b51881c901fe777e60710c7c1cd41b07647aaec519ae::server:d0b7c93b0b0d616b39ca5f849298d508 sh - --docker



