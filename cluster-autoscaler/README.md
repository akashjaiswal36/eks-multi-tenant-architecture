aws eks list-clusters

eksctl utils associate-iam-oidc-provider \
  --region ap-south-1 \
  --cluster eks-demo-cluster \
  --approve



eksctl create iamserviceaccount \
  --cluster eks-demo-cluster \
  --namespace kube-system \
  --name cluster-autoscaler \
  --attach-policy-arn arn:aws:iam::aws:policy/AutoScalingFullAccess \
  --override-existing-serviceaccounts \
  --approve


✅ Step 4: Deploy Cluster Autoscaler


  kubectl apply -f https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml

✅ Step 5: Edit deployment (VERY IMPORTANT)


  kubectl -n kube-system edit deployment cluster-autoscaler

  ✏️ Make these changes:
🔹 Replace cluster name

--cluster-name=<CLUSTER_NAME>

🔹 Ensure auto-discovery is correct

--node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/<CLUSTER_NAME>

🔹 Add region (important)

- --aws-region=ap-south-1


🔹 Add annotation (IRSA role)

Find serviceAccount section:

serviceAccountName: cluster-autoscaler

✅ Step 6: Verify

kubectl get pods -n kube-system | grep cluster-autoscaler


Check logs
kubectl logs -n kube-system deployment/cluster-autoscaler

👉 Look for:
Registered ASG

✅ Step 7: Test Autoscaling

kubectl scale deployment app-b --replicas=20 -n team-b