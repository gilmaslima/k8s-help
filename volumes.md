# Adicionando Volumes ao cluster

## Instalar o nfs server no master
sudo su -

apt install nfs-kernel-server


## Instalar o nfs common nos nodes
sudo su -

apt install nfs-common

## Criando um volume no Master e compartillhando via NFS
<code>
mkdir /opt/dados<br>
chmod 1777 /opt/dados<br>
echo "/opt/dados *(rw,sync,no_root_squash,subtree_check)" >> /etc/exports<br>
exportfs -ar
</code>
<br>
<br>

## O K3s não vem com um Provisioner por padrão, é preciso instala-lo
nano /var/lib/rancher/k3s/server/manifests/nfs.yaml

<code>
apiVersion: helm.cattle.io/v1<br>
kind: HelmChart<br>
metadata:<br>
&nbsp;  name: nfs<br>
&nbsp; namespace: default<br>
spec:<br>
&nbsp; chart: nfs-subdir-external-provisioner<br>
&nbsp; repo: https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner<br>
&nbsp; targetNamespace: default<br>
&nbsp; set:<br>
&nbsp; &nbsp; nfs.server: 192.168.0.22<br>
&nbsp; &nbsp; nfs.path: /opt/dados<br>
&nbsp; &nbsp; storageClass.name: nfs<br>
</code>
<br>

systemctl restart k3s
<br>
<br>


# Exemplo de persistence volume
<code> 
apiVersion: v1<br>
kind: PersistentVolume<br>
metadata:<br>
&nbsp; name: primeiro-pv<br>
spec:<br>
  &nbsp; capacity:<br>
  &nbsp; &nbsp; storage: 1Gi<br>
  &nbsp; accessModes:<br>
  &nbsp; - ReadWriteMany<br>
  &nbsp; persistentVolumeReclaimPolicy: Retain<br>
  &nbsp; nfs:<br>
  &nbsp; &nbsp; path: /opt/giropops<br>
  &nbsp; &nbsp; server: k8s-master<br>
  &nbsp; &nbsp; readOnly: false<br>
<br>  
</code> 

* server ->  k8s-master é o dns name de onde está sendo compartilhado o diretório, também pode ser usado o IP
* possíveis accessModes: <br>
| ReadWriteMany ->  pode ser montado por vários nodes como leitura e escrita<br>
| ReadWriteOnce -> Pode ser montado apenas uma vez e como escrita<br>
| ReadOnlyMany -> Pode ser montado por vários nodes como leitura<br>

# Exemplo de persistence volume

<code>
apiVersion: v1<br>
kind: PersistentVolumeClaim<br>
metadata:<br>
&nbsp; name: primeiro-pvc<br>
spec:<br>
&nbsp; accessModes:<br>
&nbsp; - ReadWriteMany<br>
&nbsp; storageClassName: nfs<br>
&nbsp; resources: <br>
&nbsp; &nbsp; requests:<br>
&nbsp; &nbsp; &nbsp; storage: 800Mi<br>
</code>
<br>
<br>

