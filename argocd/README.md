helm repo add argo https://argoproj.github.io/argo-helm
helm repo update


kubectl create namespace argocd

helm install argocd argo/argo-cd \
  --namespace argocd \
  --set server.service.type=LoadBalancer or ClusterIP

  kubectl get svc argocd-server -n argocd

  kubectl get secret argocd-initial-admin-secret \
  -n argocd \
  -o jsonpath="{.data.password}" | base64 -d


  