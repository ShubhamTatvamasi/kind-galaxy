# Deploy Kind Cluster

Install dependant collections:
```bash
ansible-galaxy collection install -U shubhamtatvamasi.kind
```

Install Kind cluster
```bash
ansible-playbook deploy-kind.yml -K
```
