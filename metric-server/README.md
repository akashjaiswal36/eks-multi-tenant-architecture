helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm repo update

helm install metrics-server metrics-server/metrics-server \
  -n kube-system \
  --set args[0]=--kubelet-insecure-tls \
  --set args[1]=--kubelet-preferred-address-types=InternalIP

Flag	                                           Purpose
--kubelet-insecure-tls	                      -->>      Fix TLS issue in EKS
--kubelet-preferred-address-types=InternalIP  -->>  	Use private IP (EKS nodes)

  kubectl get pods -n kube-system | grep metrics-server


  Step 4: Check Metrics API
  kubectl get apiservices | grep metrics

  test:
  kubectl top pods
kubectl top nodes



If still not working (rare case)
helm upgrade metrics-server metrics-server/metrics-server \
  -n kube-system \
  --set args={--kubelet-insecure-tls,--kubelet-preferred-address-types=InternalIP}