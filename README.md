# Flux experiment

Just capturing my experiement using Flux and getting Minikube configured with everything working, steps below.

## Minikube

https://kubernetes.io/docs/tasks/tools/install-minikube/

### Steps

```bash
minikube start \
  --cpus 6 \
  --memory 24000 \
  --disk-size 100g \
  --network-plugin=cni \
  --extra-config=kubelet.network-plugin=cni \
  --extra-config=kubelet.authentication-token-webhook=true \
  --extra-config=kubelet.authorization-mode=Webhook \
  --extra-config=scheduler.address=0.0.0.0 \
  --extra-config=controller-manager.address=0.0.0.0
```

## Calico

https://github.com/projectcalico/calico/issues/1456#issuecomment-422957446

### Steps

```bash
kubectl apply -f https://docs.projectcalico.org/v3.2/getting-started/kubernetes/installation/hosted/etcd.yaml
kubectl apply -f https://docs.projectcalico.org/v3.2/getting-started/kubernetes/installation/rbac.yaml
curl https://docs.projectcalico.org/v3.2/getting-started/kubernetes/installation/hosted/calico.yaml -O
sed -i -e "s/10\.96\.232\.136/$(kubectl get service -o json --namespace=kube-system calico-etcd | jq  -r .spec.clusterIP)/" calico.yaml
kubectl apply -f calico.yaml
```

## Flux

https://github.com/weaveworks/flux/blob/master/site/helm-get-started.md

### Steps

```bash
helm repo add weaveworks https://weaveworks.github.io/flux
kubectl apply -f https://raw.githubusercontent.com/weaveworks/flux/master/deploy-helm/flux-helm-release-crd.yaml
helm upgrade -i flux \
  --set helmOperator.create=true \
  --set helmOperator.createCRD=false \
  --set git.url=git@github.com:stefansedich/flux-demo \
  --namespace flux \
  weaveworks/flux
```

Once running get the ssh public key using `fluxctl identity` and add to GitHub
