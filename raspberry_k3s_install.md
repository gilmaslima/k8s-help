
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


