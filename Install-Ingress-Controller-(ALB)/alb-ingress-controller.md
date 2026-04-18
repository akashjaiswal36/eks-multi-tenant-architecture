Download an IAM policy for the AWS Load Balancer Controller that allows it to make calls to AWS APIs on your behalf.

curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.14.1/docs/install/iam_policy.json

Step1 -Create an IAM policy using the policy downloaded in the previous step.

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

a.Replace the values for cluster name, region code, and account ID.

eksctl create iamserviceaccount \
    --cluster=eks-demo-cluster \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --attach-policy-arn=arn:aws:iam::512741504567:policy/AWSLoadBalancerControllerIAMPolicy \
    --override-existing-serviceaccounts \
    --region ap-south-1 \
    --approve


Step 2: Install AWS Load Balancer Controller

helm repo add eks https://aws.github.io/eks-charts

a.Update your local repo to make sure that you have the most recent charts.

helm repo update eks

Install the AWS Load Balancer Controller.

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=eks-demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --version 1.14.0


  Step 3: Verify that the controller is installed
  kubectl get deployment -n kube-system aws-load-balancer-controller