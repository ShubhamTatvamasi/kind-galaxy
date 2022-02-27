# Kind Galaxy

### Development Tools

Install the following tools:

1. Docker
2. Lens
3. Homebrew (macOS)
4. Ansible

Set the following minimum resources in Docker Desktop on macOS:

![](/images/docker.png)

After Magma Orchestrator deployment the following resources will be in the use:

![](/images/lens.png)

### Deploy Kind Cluster

Install dependant collections:
```bash
ansible-galaxy collection install -U shubhamtatvamasi.kind
```

Install Kind cluster
```bash
ansible-playbook deploy-kind.yml -K
```

Create new user:
```bash
ORC_POD=$(kubectl -n orc8r get pod -l app.kubernetes.io/component=orchestrator -o jsonpath='{.items[0].metadata.name}')
kubectl -n orc8r exec -it ${ORC_POD} -- envdir /var/opt/magma/envdir /var/opt/magma/bin/accessc \
  add-existing -admin -cert /var/opt/magma/certs/admin_operator.pem admin_operator

NMS_POD=$(kubectl -n orc8r get pod -l app.kubernetes.io/component=magmalte -o jsonpath='{.items[0].metadata.name}')
kubectl -n orc8r exec -it ${NMS_POD} -- yarn setAdminPassword magma-test admin admin
kubectl -n orc8r exec -it ${NMS_POD} -- yarn setAdminPassword master admin admin
```

Update these values in `/etc/hosts` file:
```
127.0.0.1 api.magma.local
127.0.0.1 fluentd.magma.local
127.0.0.1 controller.magma.local
127.0.0.1 master.nms.magma.local
127.0.0.1 magma-test.nms.magma.local
127.0.0.1 bootstrapper-controller.magma.local
```

https://magma-test.nms.magma.local \
https://api.magma.local/swagger/v1/ui/
