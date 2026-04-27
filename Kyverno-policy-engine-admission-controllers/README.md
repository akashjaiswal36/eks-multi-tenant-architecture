👉 Kyverno = Kubernetes-native policy engine

It lets you:

Enforce rules (like security guard 🚔)
Modify resources automatically
Validate configurations

👉 Example:

“All pods must have resource limits”
“No containers should run as root”

1️⃣ Validate (most common)
disallow pods without resources

2️⃣ Mutate
add labels automatically

3️⃣ Generate
create NetworkPolicy when namespace is created

🎯 Interview Answer (use this)
“Kyverno is a Kubernetes-native policy engine that allows validation, mutation, and generation of resources using YAML-based policies.”

----------------------------------------------------
🚀 PART 2 — Hands-on Practice (your EKS)

✅ Step 1: Install Kyverno

✅ Correct Way to Install Kyverno
🚀 Option 1 (RECOMMENDED): Install via Helm

This is the official and stable way

helm repo remove kyverno
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update


helm install kyverno kyverno/kyverno \
  -n kyverno \
  --create-namespace \
  --set backgroundController.sanityChecks=true

kubectl get pods -n kyverno
------------------------------------------------------------

🧪 Step 2: First Policy (VALIDATE)

👉 Block pods without CPU limits

Create file: require-cpu.yaml

apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-cpu-limits
spec:
  validationFailureAction: Enforce
  rules:
    - name: check-cpu-limits
      match:
        resources:
          kinds:
            - Pod
      validate:
        message: "CPU limits are required"
        pattern:
          spec:
            containers:
              - resources:
                  limits:
                    cpu: "?*"


kubectl apply -f require-cpu.yaml

🧪 Step 3: Test it (IMPORTANT)
❌ Create bad pod (should fail)

kubectl run test-pod --image=nginx