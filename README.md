# Installing and Running Anible AWX on Kubernetes

📺 Watch the Full Tutorial

[![Ansible AWX on Kubernetes](https://img.youtube.com/vi/FZcXjovCnOw/0.jpg)](https://www.youtube.com/watch?v=FZcXjovCnOw)  

📌 Overview

This repository contains all the necessary files and configurations to install and run Ansible AWX on Kubernetes using the AWX Operator.

🚀 Features

✅ Deploy Ansible AWX on Kubernetes using AWX Operator

✅ Configure Ingress for external access

✅ Access the AWX Web UI via ansible-awx.devopstips.local

✅ Secure login with admin user


🛠 Prerequisites

Ensure you have the following installed:

✅ A running Kubernetes cluster (K3s, Minikube, K3d or any cloud provider)

✅ kubectl and helm installed

A working Ingress Controller (e.g., Nginx, Traefik)

📥 Installation Steps

1️⃣ Deploy the AWX Operator
```bash
git clone https://github.com/ansible/awx-operator.git
cd awx-operator
git tag
git checkout tags/2.19.1
export VERSION=2.19.1

nano kustomization.yaml

apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - github.com/ansible/awx-operator/config/default?ref=2.19.1
  - awx-demo.yml

images:
  - name: quay.io/ansible/awx-operator
    newTag: 2.19.1
namespace: awx
```
2️⃣ Deploy AWX
```bash
kubectl apply -k .
```
3️⃣ Configure Ingress

```bash
nano awx-ingress.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: awx-ingress
  namespace: awx
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: ansible-awx.devopstips.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: awx-demo-service
            port:
              number: 80
```

4️⃣ Update Hosts File
```bash
echo "127.0.01 ansible-awx.devopstips.local" | sudo tee -a /etc/hosts
```
5️⃣ Retrieve Admin Password
```bash
kubectl get secret awx-admin-password -n awx -o jsonpath='{.data.password}' | base64 --decode
```
🌐 Accessing the AWX Web UI

📌 Open your browser and go to: http://ansible-awx.devopstips.local

👤 Login Credentials:

Username: admin

Password: (Retrieve using the above command)

📢 Contribute & Support

Feel free to fork this repository, create issues, or contribute via PRs! If this helped you, consider subscribing to my YouTube channel for more DevOps content. 🚀

Happy Automation! 🤖

# AWX Operator Documentation

The AWX Operator documentation is now available at https://awx-operator.readthedocs.io/

For docs changes, create PRs on the appropriate files in the /docs folder.

## Contributing

Please visit [our contributing guidelines](https://github.com/ansible/awx-operator/blob/devel/CONTRIBUTING.md).


## Author

This operator was originally built in 2019 by [Jeff Geerling](https://www.jeffgeerling.com) and is now maintained by the Ansible Team

## Code of Conduct

We ask all of our community members and contributors to adhere to the [Ansible code of conduct](http://docs.ansible.com/ansible/latest/community/code_of_conduct.html). If you have questions or need assistance, please reach out to our community team at [codeofconduct@ansible.com](mailto:codeofconduct@ansible.com)

