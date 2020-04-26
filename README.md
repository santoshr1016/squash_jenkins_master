### Create Jenkins Master as a Service

I am using Digital Ocean K8S, DO is so yummy!!!! Just loving it.


```text
The idea is to provision the Jenkins master infrastructure with persistent volume.
When a job (sequential and parallel) is build, Dynamically the workloads pods will be created to run that build
and the artifacts will be stored in PV and those pods will be deleted.
```

### Step 1 Deploying the NFS Server with Helm
```text
helm fetch --untar stable/nfs-server-provisioner
cd nfs-server-provisioner
helm install --name nfs-server .  --set persistence.enabled=true,persistence.storageClass=do-block-storage,persistence.size=50Gi
helm install --name nfs-server nfs-server-provisioner  --set persistence.enabled=true,persistence.storageClass=do-block-storage,persistence.size=50Gi
helm fetch --untar stable/jenkins
helm install --name my-jenkins jenkins --values jenkins/values.yaml
helm status my-jenkins
watch k get pods
k logs -f my-jenkins-XXX
```

```text
1. Get your 'admin' user password by running:
  printf $(kubectl get secret --namespace default my-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services my-jenkins)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo http://$NODE_IP:$NODE_PORT/login

3. Login with the password from step 1 and the username: admin
```