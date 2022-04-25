# Secrets - Comandos

echo -n "descomplicando-k8s" > secret.txt

kubectl create secret generic my-secret --from-file=secret.txt

kubectl describe secret my-secret

kubectl get secret

kubectl get secret my-secret -o yaml

echo 'ZGVzY29tcGxpY2FuZG8tazhz' | base64 --decode


<br>

## Pod com secret file

vim pod-secret.yaml

<code>

apiVersion: v1<br>
kind: Pod<br>
metadata:<br>
&nbsp; name: teste-secret<br>
&nbsp; namespace: default<br>
spec:<br>
&nbsp; containers:<br>
&nbsp; - image: busybox<br>
&nbsp; &nbsp;  name: busy<br>
&nbsp; &nbsp; command:<br>
&nbsp; &nbsp; &nbsp; - sleep<br>
&nbsp; &nbsp; &nbsp; - "3600"<br>
&nbsp; &nbsp; volumeMounts:<br>
&nbsp; &nbsp; - mountPath: /tmp/giropops<br>
&nbsp; &nbsp; name: my-volume-secret<br>
&nbsp; volumes:<br>
&nbsp; - name: my-volume-secret<br>
&nbsp; &nbsp; secret:<br>
&nbsp; &nbsp; &nbsp; secretName: my-secret<br>

</code>

<br>

## Criando o secret literal

kubectl create -f pod-secret.yaml<br>
kubectl exec -ti teste-secret -- ls /tmp/giropops

kubectl exec -ti teste-secret -- cat /tmp/giropops/secret.txt<br>

kubectl create secret generic my-literal-secret --from-literal user=linuxtips --from-literal password=catota<br>
kubectl describe secret my-literal-secret<br>

<br>


## Pod usando o literal como env

vim pod-secret-env.yaml<br>

<code>
apiVersion: v1<br>
kind: Pod<br>
metadata:<br>
&nbsp; name: teste-secret-env<br>
&nbsp; namespace: default<br>
spec:<br>
&nbsp; containers:<br>
&nbsp; - image: busybox<br>
&nbsp; &nbsp; name: busy-secret-env<br>
&nbsp; &nbsp; command:<br>
&nbsp; &nbsp; &nbsp; - sleep<br>
&nbsp; &nbsp; &nbsp; - "3600"<br>
&nbsp; &nbsp; env:<br>
&nbsp; &nbsp; - name: MEU_USERNAME<br>
&nbsp; &nbsp; &nbsp; valueFrom:<br>
&nbsp; &nbsp; &nbsp; &nbsp; secretKeyRef:<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; name: my-literal-secret<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; key: user<br>
&nbsp; &nbsp; - name: MEU_PASSWORD<br>
&nbsp; &nbsp; &nbsp; valueFrom:<br>
&nbsp; &nbsp; &nbsp; &nbsp; secretKeyRef:<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; name: my-literal-secret<br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; key: password<br>

</code>          
<br>
kubectl create -f pod-secret-env.yaml<br>
kubectl exec teste-secret-env -c busy-secret-env -it -- printenv<br>
