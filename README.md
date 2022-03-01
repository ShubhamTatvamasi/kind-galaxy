# Kind Galaxy

### Development Tools

Install the following tools:

1. Docker
2. Lens
3. Homebrew (macOS)
4. Ansible

Install **Ansible** and **gnu-tar** packages:
```bash
brew install ansible gnu-tar
```

Set the following minimum resources in Docker Desktop on macOS:

![](/images/docker.png)

After Magma Orchestrator deployment the following resources will be in the use:

![](/images/lens.png)

### Deploy Kind Cluster

Install dependant collections:
```bash
ansible-galaxy collection install -U shubhamtatvamasi.kind
```

Install Magma Orchestrator on Kind cluster:

> :warning: **Note:** Make sure you don't have anything running on port **80** and **433** in your local machine.

```bash
ansible-playbook deploy-kind.yml -K
```

Create new user with ID `admin` and password `admin`:

> :warning: **Note:** Exectue the commands below one by one.

```bash
ORC_POD=$(kubectl -n orc8r get pod -l app.kubernetes.io/component=orchestrator -o jsonpath='{.items[0].metadata.name}')
kubectl -n orc8r exec -it ${ORC_POD} -- envdir /var/opt/magma/envdir /var/opt/magma/bin/accessc \
  add-existing -admin -cert /var/opt/magma/certs/admin_operator.pem admin_operator

NMS_POD=$(kubectl -n orc8r get pod -l app.kubernetes.io/component=magmalte -o jsonpath='{.items[0].metadata.name}')
kubectl -n orc8r exec -it ${NMS_POD} -- yarn setAdminPassword magma-test admin admin
kubectl -n orc8r exec -it ${NMS_POD} -- yarn setAdminPassword master admin admin
```

### NMS Dashboard

Update these values in `/etc/hosts` file:
```
127.0.0.1 api.magma.local
127.0.0.1 fluentd.magma.local
127.0.0.1 controller.magma.local
127.0.0.1 master.nms.magma.local
127.0.0.1 magma-test.nms.magma.local
127.0.0.1 bootstrapper-controller.magma.local
```

Open the NMS dashboard with the following URL:

https://magma-test.nms.magma.local/

### Swagger API

You can find all the secrets at:
```bash
ls ~/magma-galaxy/secrets
```

Upload `admin_operator.pfx` file to Keychain Acesss with the following password:
```
password
```

Then access the Swagger API with the following URL:

https://api.magma.local/swagger/v1/ui/

### Uninstall

Delete Magma Orchestrator Kind cluster:
```bash
kind delete cluster --name orc8r
```

### FAQ

Q. Got the following error on Ubuntu 20.04 LTS:

```
fatal: [localhost]: FAILED! => {"msg": "Could not find imported module support code for ansiblemodule. Looked for either AnsibleTurboModule.py or module.py"}
```

A. Install the latest version of Ansible:

```bash
sudo apt remove ansible
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```
