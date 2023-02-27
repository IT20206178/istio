# install/Update istioctl
### To upgrade change the ISTIO_VERSION as needed
```
curl -sL https://istio.io/downloadIstioctl | ISTIO_VERSION=1.16.2 sh -
export PATH=$HOME/.istioctl/bin:$PATH
istioctl version

istioctl x precheck
istioctl operator init --hub=gcr.io/istio-release
istioctl proxy-status
```
### Use below command to enable istio sidecar injection manually
```
system_namespaces=("kube-system" "default"  "istio-system")
namespaces=`kubectl get ns | tail -n +2 | cut -d ' ' -f 1`
for ns in $namespaces; do 
  if [[ ! " ${system_namespaces[*]} " =~ " ${ns} " ]]; then
    kubectl label namespace $ns istio-injection=enabled --overwrite
  fi
done
```
### Use below command to restart deployments
```
namespaces=`kubectl get ns -l istio-injection=enabled | tail -n +2 | cut -d ' ' -f 1`
for ns in $namespaces; do
  kubectl -n $ns rollout restart deploy
done
```
