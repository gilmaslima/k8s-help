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

```
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata:
  name: nfs
  namespace: default
spec:
  chart: nfs-subdir-external-provisioner
  repo: https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner
  targetNamespace: default
  set:
    nfs.server: 192.168.0.22
    nfs.path: /opt/dados
    storageClass.name: nfs
```
<br>

systemctl restart k3s
<br>
<br>


# Exemplo de persistence volume


```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: primeiro-pv
spec:
  capacity:
    storage: 1Gi
    accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /opt/giropops
    server: k8s-master
    readOnly: false

```
<br>

* server ->  k8s-master é o dns name de onde está sendo compartilhado o diretório, também pode ser usado o IP
* possíveis accessModes: <br>
| ReadWriteMany ->  pode ser montado por vários nodes como leitura e escrita<br>
| ReadWriteOnce -> Pode ser montado apenas uma vez e como escrita<br>
| ReadOnlyMany -> Pode ser montado por vários nodes como leitura<br>

# Exemplo de persistence volume

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: primeiro-pvc
spec:
  accessModes:
  - ReadWriteMany
  storageClassName: nfs
  resources:
    requests:
      storage: 800Mi
```

<br>
<br>

