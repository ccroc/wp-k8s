# Start minikube cluster
```sh
minikube start --nodes 2 -p wordpress
```

# Get kubeconfig
```sh
kubectl config view 
```

# Create Persistent Volumes
REF: https://github.com/IBM/Scalable-WordPress-deployment-on-Kubernetes/blob/master/local-volumes.yaml
First of all create PV, one for WordPress and another for MySQL
```
kubectl apply -f ./pv.yaml
kubectl apply -f ./pvc.yaml

# Note: delete storage class if exists, othrwise PVC not bind PV
```

# Deployments
### MySQL
REF: curl -LO https://k8s.io/examples/application/wordpress/mysql-deployment.yaml
```
kubectl apply -f ./mysql-deployment.yaml
```
### WordPress
REF: curl -LO https://k8s.io/examples/application/wordpress/wordpress-deployment.yaml
```
kubectl apply -f ./wordpress-deployment.yaml
```

# Password
```sh
echo -n 'admin' | base64
```
Insert the result into secret.yaml
```sh
kubectl apply -f ./secret.yaml
```

```sh
cat <<EOF >./kustomization.yaml
secretGenerator:
- name: mysql-pass
  literals:
  - password=carlo
EOF

cat <<EOF >>./kustomization.yaml
resources:
  - mysql-deployment.yaml
  - wordpress-deployment.yaml
EOF
```

# RUN ALL
```sh
kubectl apply -k ./
```