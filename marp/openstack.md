---
title:       OpenStack
description: OpenStack & Ceph
author:      infraguide.org
keywords:    openstack,ceph,workshops,operations,troubleshoouting
url:
image:
backgroundImage: url(../assets/marp/background.jpg)
transition: cover
paginate: true
footer: "[infraguide.org](https://infraguide.org) | Questions or need a guided workshop? → ping@infraguide.org | [Imprint](https://infraguide.org/imprint/) · [Privacy](https://infraguide.org/privacy/)"
style: |
  footer {
    font-size: 7px;
    text-align: left;
  }
  .mermaid {
    text-align: center;
  }
  .mermaid svg {
    width:  50%;
    height: 50%;    
  }
---
<script type="module">
import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs';
mermaid.initialize({ startOnLoad: true, theme: 'default' });
</script>

# OpenStack
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)


[//]: # (https://docs.redhat.com/en/documentation/red_hat_openstack_platform/9/html/architecture_guide/components)
[//]: # (https://cloudification.io/cloud-blog/openstack-microservice-architecture-main-components/)
[//]: # (https://canonical-openstack.readthedocs-hosted.com/en/latest/explanation/)
[//]: # (https://www.freecodecamp.org/news/openstack-tutorial-operate-your-own-private-cloud/)
[//]: # (https://superuser.openinfra.org/articles/kolla-ansible-openstack-installation-ubuntu-24-04/)

[//]: # (TODO: monitoring - node-exporter full)
[//]: # (TODO: monitoring - cadvisor)
[//]: # (TODO: monitoring - libvirt)
[//]: # (TODO: monitoring - Prom)
[//]: # (TODO: monitoring - Alertmanager)
[//]: # (TODO: monitoring - RabbitMQ)


[//]: # (Architecture: https://pure-storage-openstack-docs.readthedocs.io/en/zed_a/_images/openstack_architecture.png )
[//]: # (Architecture: https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_897/https%3A%2F%2Fassets.ubuntu.com%2Fv1%2F27493de3-Canonical%20OpenStack%20architecture%20diagram.png )

---
# Targets
- Deployment
- Operations
- Networking
- Monitoring
- Troubleshooting
- Automation
- Testing

---
# OpenStack Distributions
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
[Catalog](https://www.openstack.org/marketplace/distros/)

- [OSISM](https://www.openstack.org/marketplace/distros/distribution/osism/osism-distro)
- [Mirantis](https://www.openstack.org/marketplace/distros/distribution/mirantis/mirantis-openstack-for-kubernetes)
- [Canonical](https://www.openstack.org/marketplace/distros/distribution/canonical/canonical-openstack)
- [RedHat](https://docs.redhat.com/en/documentation/red_hat_openstack_services_on_openshift/18.0/html/planning_your_deployment/assembly_red-hat-openstack-services-on-openshift-overview#con_alternate-topo-rhoso_rhoso-overview)
- [FishOS](https://www.openstack.org/marketplace/distros/distribution/sardina-systems/fishos)

---
# Architecture
![ bg right:65% 80%](https://www.openstack.org/static/000588f8b89d94da80eba6101f72ff7a/openstack-map-v20240401.png)

---
# Architecture
![ bg right:65% 80%](https://www.redhat.com/rhdc/managed-files/sysadmin/2021-08/Ceph-storage-cluster-%28RADOS%29.png)

---
# Architecture
![ bg right:65% 80%](https://access.redhat.com/webassets/avalon/d/Red_Hat_OpenStack_Platform-9-Architecture_Guide-en-US/images/fce6394275bd3444892c5d3a91ccf17c/RHEL_OSP_arch_347192_1015_JCS_01_Interface-Overview.png)

---
# Architecture
![ bg right:65% 80%](https://access.redhat.com/webassets/avalon/d/Red_Hat_OpenStack_Platform-9-Architecture_Guide-en-US/images/c6afcf33c1422e761f467c40b6980396/RHEL_OSP_arch_347192_1015_JCS_Ex-Basic-Arch.png)

---
# Architecture
![ bg right:65% 80%](https://access.redhat.com/webassets/avalon/d/Red_Hat_OpenStack_Platform-9-Architecture_Guide-en-US/images/ef505e423b37c7cbecbe6dfa4021f384/RHEL_OSP_arch_347192_1015_JCS_04_Interface-Identity.png)

---
# Architecture
![ bg right:65% 80%](https://access.redhat.com/webassets/avalon/d/Red_Hat_OpenStack_Platform-8-Architecture_Guide-en-US/images/661ccaef79099e48e1d4e33e48e52cfc/RHEL_OSP_arch_347192_1015_JCS_05_Interface-Networking.png)

---
# Architecture
![ bg right:65% 80%](https://access.redhat.com/webassets/avalon/d/Red_Hat_OpenStack_Platform-8-Architecture_Guide-en-US/images/aa867c517b4e6f46edf34efb04eba1cb/RHEL_OSP_arch_347192_1015_JCS_06_Interface-Block-Storage.png)

---
# Architecture
![ bg right:65% 80%](https://access.redhat.com/webassets/avalon/d/Red_Hat_OpenStack_Platform-8-Architecture_Guide-en-US/images/59429a3c3b9440fe0441da62a3095a9a/RHEL_OSP_arch_347192_1015_JCS_07_Interface-Compute.png)

---
# Architecture
![ bg right:65% 80%](https://access.redhat.com/webassets/avalon/d/Red_Hat_OpenStack_Platform-8-Architecture_Guide-en-US/images/3d273bee788ec5deb1fd11a22b7e1487/RHEL_OSP_arch_347192_1015_JCS_08_Interface-Image.png)

---
# Architecture
![ bg right:65% 80%](https://access.redhat.com/webassets/avalon/d/Red_Hat_OpenStack_Platform-9-Architecture_Guide-en-US/images/05df8dacdfb319319665befc022e4159/RHEL_OSP_arch_347192_1015_JCS_02_Interface-Dashboard.png)


---
# Users

![bg right:75% 80%](https://www.svgrepo.com/show/303419/walmart-logo.svg)
![bg right:75% 80%](https://www.svgrepo.com/show/303330/nasa-6-logo.svg)
![bg right:75% 80%](https://upload.wikimedia.org/wikipedia/de/c/c2/Huawei-Logo.svg)
![bg right:75% 80%](https://www.svgrepo.com/show/331321/blizzard.svg)
![bg right:75% 80%](https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_239/https%3A%2F%2Fassets.ubuntu.com%2Fv1%2F57c722c5-cern-logo.png)
![bg right:75% 80%](https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_313/https%3A%2F%2Fassets.ubuntu.com%2Fv1%2F60fd1f45-deutsche-telekom.png)
![bg right:75% 80%](https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_313/https%3A%2F%2Fassets.ubuntu.com%2Fv1%2F1e543d4d-Bloomberg-Logo.png)

---
# Contributors
![bg right:60% 80%](https://www.svgrepo.com/show/354271/redhat.svg)
![bg right:60% 80%](https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_348/https%3A%2F%2Fassets.ubuntu.com%2Fv1%2F1c72c15a-canonical-logo.png)
![bg right:60% 80%](https://upload.wikimedia.org/wikipedia/commons/a/a4/Mirantis_logo.svg)
![bg right:60% 80%](https://upload.wikimedia.org/wikipedia/de/c/c2/Huawei-Logo.svg)
![bg right:60% 80%](https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_189/https%3A%2F%2Fassets.ubuntu.com%2Fv1%2Fc3382d32-cisco-logo.png)
![bg right:60% 80%](https://www.svgrepo.com/show/303630/nvidia-logo.svg)
![bg right:60% 80%](https://www.svgrepo.com/show/349412/intel.svg)


---
# Deployment Approaches
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
- openstack-ansible
- kolla-ansible
- OpenStack on OpenShift
- OpenStack-Helm
- OSISM
- YAOOK
...

---
# Deploy OpenStack
![bg right:30% 90%](https://superuser.openinfra.org/wp-content/uploads/2025/01/1_q6dlalwfoVWwUqFmo4I-9g.png)

---
# OpenStack Network
![bg right:50% 90%](https://infraguide.org/assets/img/openstack/env.svg)

---
# Environment Network
![bg right:50% 90%](../assets/marp/openstack-environment.drawio.svg)

---
# Air-Gapped Environment
![bg right:50% 90%](https://upload.wikimedia.org/wikipedia/commons/d/da/Air_gap_network.png)

---
# Workshop Hardware and VMs
* Hardware: 60 vCPUs, 160 GB RAM, 1 TB disk
* Deployment VM
* Infra VMs: registry, proxy, dns, recorder
* OpenStack VMs: 3x Controller, 3x Network, 3(+)x Compute, 1xMonitoring, 1xTesting 

---
# Infrastructure
<div class="mermaid">
graph LR
    internet[Internet <img src="https://www.svgrepo.com/show/243037/clouds-cloud.svg" width="60"/>]
    subgraph Infrastructure
        direction TB
        registry("Registry\n(Harbor)")
        dns("DNS\n(dnsmasq)")
        proxy("Proxy\n(squid)")
        recorder("Recorder\n(ansible/ara)")
    end
    subgraph Environment
        direction TB
        os(OpenStack)
        deploy("Deployment")
    end
    %% Connections
    dns --> internet
    registry --> internet
    proxy --> internet
    os --> registry
    os --> dns
    deploy --> proxy
    deploy --> dns
    deploy --> recorder
    %%Environment --> Infrastructure
    %%registry --> os
</div>

---
# Deployment Node
* Python virtual envs
* kolla-ansible


---
# Deploy OpenStack
![bg right:30% 90%](https://superuser.openinfra.org/wp-content/uploads/2025/01/1_q6dlalwfoVWwUqFmo4I-9g.png)

```bash
# Prepare Python virtual environment                                        📋
cd ~/openstack/

python3 -m venv $VENV_PATH

ln -s  $VENV_PATH ~/venv/openstack
source $VENV_PATH/bin/activate

# Install needed pip packages
pip install -U pip
pip install -r requirements.txt

# Install dependencies for kolla-ansible
kolla-ansible install-deps

# ⚠️ NOTE: fix for prometheus 05.2026
# pip install "bcrypt<5.0.0"

# Install additional dependencies
gilt overlay

# Reload environment
source ~/.profile
```

---
# Deploy OpenStack
![bg right:30% 90%](https://superuser.openinfra.org/wp-content/uploads/2025/01/1_q6dlalwfoVWwUqFmo4I-9g.png)
```bash
# Prepare ansible inventory                                                 📋

# Copy inventory file
cp ~/venv/openstack/share/kolla-ansible/ansible/inventory/multinode inventory/

# Patch inventory file and review inventory/wrx/ directory afterwards
crudini --del inventory/multinode control
crudini --del inventory/multinode network
crudini --del inventory/multinode compute
crudini --del inventory/multinode monitoring
crudini --del inventory/multinode storage
crudini --del inventory/multinode loadbalancer:children

# Prepare passwords
cp ~/venv/openstack/share/kolla-ansible/etc_examples/kolla/passwords.yml \
  custom-config/wrx/passwords.yml

kolla-genpwd -p custom-config/wrx/passwords.yml
```

---
# Deploy OpenStack
![bg right:30% 90%](https://superuser.openinfra.org/wp-content/uploads/2025/01/1_q6dlalwfoVWwUqFmo4I-9g.png)
```bash
# Overview of kolla-ansible commands                                        📋

kolla-ansible bootstrap-servers    -i inventory/wrx
kolla-ansible octavia-certificates -i inventory/wrx
kolla-ansible prechecks            -i inventory/wrx
kolla-ansible pull                 -i inventory/wrx
kolla-ansible deploy               -i inventory/wrx  -vvvv
kolla-ansible post-deploy          -i inventory/wrx
kolla-ansible check                -i inventory/wrx
kolla-ansible destroy              -i inventory/wrx <--yes-i-really-really-mean-it>
kolla-ansible mariadb_recovery     -i inventory/wrx
kolla-ansible reconfigure          -i inventory/wrx
kolla-ansible upgrade              -i inventory/wrx
kolla-ansible stop                 -i inventory/wrx
kolla-ansible deploy-containers    -i inventory/wrx
kolla-ansible prune-images         -i inventory/wrx
```

---
# Deploy OpenStack
![bg right:30% 90%](https://superuser.openinfra.org/wp-content/uploads/2025/01/1_q6dlalwfoVWwUqFmo4I-9g.png)
```bash
# Prepare the kolla-ansible deployment                                      📋

kolla-ansible bootstrap-servers    -i inventory/wrx
kolla-ansible octavia-certificates -i inventory/wrx
kolla-ansible prechecks            -i inventory/wrx

# ⚠️ NOTE: this will take time
kolla-ansible pull                 -i inventory/wrx
```

---
# Deploy OpenStack
![bg right:30% 90%](https://superuser.openinfra.org/wp-content/uploads/2025/01/1_q6dlalwfoVWwUqFmo4I-9g.png)
```bash
# Prepare the kolla-ansible deployment                                      📋

# ⚠️ NOTE: this will take time
kolla-ansible deploy               -i inventory/wrx

# Post deployment steps
kolla-ansible post-deploy          -i inventory/wrx
kolla-ansible check                -i inventory/wrx
```

---
# Bootstrap a Test Environment
![bg right:30% 90%](https://superuser.openinfra.org/wp-content/uploads/2025/01/1_q6dlalwfoVWwUqFmo4I-9g.png)
```bash
# Create first basic test resources                                         📋
KOLLA_CONFIG_PATH=~/openstack/custom-config/wrx/    \
ENABLE_EXT_NET=1                                    \
EXT_NET_CIDR=10.30.0.0/24                           \
EXT_NET_GATEWAY=10.30.0.1                           \
EXT_NET_RANGE="start=10.30.0.240,end=10.30.0.245"   \
DEMO_NET_DNS=10.30.0.1                              \
~/venv/openstack/share/kolla-ansible/init-runonce
```


---
# Access
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
<style scoped>
table, thead, tbody, tr, th, td {
  background-color: transparent !important;
  border: none !important;
}
</style>
| | |
| :--- | :--- |
| Horizon     | [http://int.os.wrx.sckt.net](http://int.os.wrx.sckt.net/)      |
| Skyline     | [http://int.os.wrx.sckt.net:9999](http://int.os.wrx.sckt.net/) |
| CLI         | Command Line Interface                  |


Check the following files to use with CLI:
- custom-config/wrx/admin-openrc.sh
- custom-config/wrx/clouds.yaml

---
# Operations
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)

---
# Service Overview
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Review the OpenStack environment                                          📋
openstack service  list
openstack endpoint list
openstack catalog  list

openstack compute       service list
openstack volume        service list 
openstack network       agent   list
openstack orchestration service list
```

---
# Replace a Network Node
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# 🔍 Review data stored on the node. Is there anything to back up?          📋
docker volume ls
du -lhs /var/lib/docker/volumes/*

# 💥 Break the network03 node
apt remove  docker*  -y
rm -rf  /etc/kolla /var/log/kolla  \
        /etc/systemd/system/kolla* \
        /etc/systemd/system/multi-user.target.wants/kolla-* \
        /etc/docker/daemon.json
umount /var/lib/docker;  wipefs /dev/sdb -a; mkfs.ext4 /dev/sdb
reboot

# 🚚 Redeploy OpenStack services on network03 from the deployment node
kolla-ansible bootstrap-servers -i inventory/wrx --limit ~network03
kolla-ansible prechecks         -i inventory/wrx --limit ~network03
kolla-ansible pull              -i inventory/wrx --limit ~network03
kolla-ansible reconfigure       -i inventory/wrx --limit ~network03
```
---
# Replace a Controller Node
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# 🔍 Review data stored on the node. Is there anything to back up?          📋
docker volume ls
du -lhs /var/lib/docker/volumes/*

# 💥 Break the controller03 node
systemctl stop kolla-* docker.service docker.socket
apt remove  docker*  -y
rm -rf  /etc/kolla /var/log/kolla  \
        /etc/systemd/system/kolla* \
        /etc/systemd/system/multi-user.target.wants/kolla-* \
        /etc/docker/daemon.json
umount /var/lib/docker;  wipefs /dev/sdb -a; mkfs.ext4 /dev/sdb
reboot

# 🚚 Redeploy OpenStack services on controller03 from the deployment node
kolla-ansible bootstrap-servers -i inventory/wrx --limit ~controller03
kolla-ansible prechecks         -i inventory/wrx --limit ~controller03
kolla-ansible pull              -i inventory/wrx --limit ~controller03

# ☕ this will take a while
kolla-ansible reconfigure       -i inventory/wrx --limit ~controller03 --skip-tags keystone

# ⚠️ we have to reconfigure keystone across all controller nodes ⚠️
kolla-ansible reconfigure       -i inventory/wrx                       --tags      keystone
```

---
# Replace a Compute Node
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# 🔍 Review data stored on the node. Is there anything to back up?          📋
docker volume ls
du -lhs /var/lib/docker/volumes/*
docker exec -ti nova_libvirt virsh list  --all

# 🚧 Drain the OpenStack compute host
openstack server list --all       --host    <COMPUTE_NAME>
openstack compute service set     --disable <COMPUTE_NAME> nova-compute
for vm in $(openstack server list --host    <COMPUTE_NAME> --all-projects -c ID -f value);
do
  echo "Evacuating VM: $vm"
  openstack server migrate --live $vm
done
openstack server list --all       --host    <COMPUTE_NAME>

# 💥 Break the node
# On the node
systemctl stop kolla-* docker.service docker.socket
apt remove  docker*  -y
rm -rf  /etc/kolla /var/log/kolla  \
        /etc/systemd/system/kolla* \
        /etc/systemd/system/multi-user.target.wants/kolla-* \
        /etc/docker/daemon.json
cp -pr  /var/lib/docker/volumes ~/
umount  /var/lib/docker;  wipefs /dev/sdb -a; mkfs.ext4 /dev/sdb
reboot

# 🚚 Redeploy OpenStack services on compute03 from the deployment node
kolla-ansible bootstrap-servers -i inventory/wrx --limit ~<COMPUTE_NAME>
kolla-ansible prechecks         -i inventory/wrx --limit ~<COMPUTE_NAME>
kolla-ansible pull              -i inventory/wrx --limit ~<COMPUTE_NAME>
kolla-ansible reconfigure       -i inventory/wrx --limit ~<COMPUTE_NAME>
```

---
# Debugging
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# 🩺 Check                                                                  📋
openstack compute service list

# 🔍 Take a look at logs on compute03
tail -f /var/log/kolla/nova/nova-compute.log

# 🔍 Compare files on nova_compute volume and in the backup
ls -lh ~/volumes/nova_compute/_data/ /var/lib/docker/volumes/nova_compute/_data/

# ⚙️ Repair
cp ~/volumes/nova_compute/_data/compute_id  /var/lib/docker/volumes/nova_compute/_data/

# 🩺 Check the status again and test
openstack compute service list
openstack compute service set --enable compute03 nova-compute
openstack server migrate --live --host compute03 <SERVER_ID> 
openstack server list    --all  --long
```
---
# Scale-out
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
* How to scale out controllers?
* How to scale out network nodes?
* How to scale out compute nodes?

---
# Add a New Compute Host
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# 📝 Modify inventory, put the new compute host into the hypervisor section 📋
vim inventory/wrx/20_openstack

# 🩺 Check the new host

# 🚚 Deploy OpenStack services on the new compute host
kolla-ansible bootstrap-servers -i inventory/wrx --limit ~compute12
kolla-ansible prechecks         -i inventory/wrx --limit ~compute12
kolla-ansible pull              -i inventory/wrx --limit ~compute12
kolla-ansible deploy            -i inventory/wrx --limit ~compute12

# 🩺 Check the new host
openstack compute service list
openstack server  migrate --live --host compute12 <SERVER_ID>

docker exec -ti nova_libvirt virsh list  --all # on the host
```

---
# Remove a Compute Host
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)

---
# Reboot an OpenStack Host
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
* How to reboot controllers?
* How to reboot network nodes?
* How to reboot compute nodes?

---
# Debugging RabbitMQ
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)

```bash
# Some commands to debug RabbitMQ errors                                    📋

rabbitmqctl status
rabbitmqctl cluster_status

rabbitmq-plugins list
rabbitmq-plugins disable <PLUGIN>
rabbitmq-plugins enable  <PLUGIN>

rabbitmqctl list_queues name,messages,messages_ready,messages_unacknowledged
rabbitmqctl list_bindings 

rabbitmqctl purge_queue   <QUEUE_NAME>
rabbitmqctl delete_queue  <QUEUE_NAME>
```

---
# Upgrade
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)

---
# SLURP Model
![bg right:50% 90%](https://releases.openstack.org/_images/slurp.png)

---
# Roll out the Next Release
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# 📝 Update kolla-ansible                                                   📋
CUR_KA_VER=$(pip list | grep kolla | awk '{print $NF}' | awk -F . '{print $1}')

echo $VIRTUAL_ENV
deactivate
python3  -m venv ~/venv/kolla-ansible-$((CUR_KA_VER+1)) 
source  ~/venv/kolla-ansible-$((CUR_KA_VER+1))/bin/activate

pip install -U pip
pip install -r requirements.txt
pip install -U "kolla-ansible<$((CUR_KA_VER + 2)).0"
kolla-ansible install-deps
# ⚠️ NOTE: fix for prometheus 05.2026
# pip install "bcrypt<5.0.0"

```

---
# Roll out the Next Release
## Inventory
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Update inventory                                                          📋
mv inventory/multinode inventory/multinode.old
cp $VIRTUAL_ENV/share/kolla-ansible/ansible/inventory/multinode inventory/
crudini --del inventory/multinode control
crudini --del inventory/multinode network
crudini --del inventory/multinode compute
crudini --del inventory/multinode monitoring
crudini --del inventory/multinode storage
crudini --del inventory/multinode loadbalancer:children

# Review the differences
diff -y --suppress-common-lines  inventory/multinode  inventory/multinode.old
```

---
# Roll out the Next Release
## Passwords
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Update passwords                                                          📋
mv custom-config/wrx/passwords.yml custom-config/wrx/passwords.yml.old
cp $VIRTUAL_ENV/share/kolla-ansible/etc_examples/kolla/passwords.yml \
    custom-config/wrx/passwords.yml
kolla-genpwd -p custom-config/wrx/passwords.yml
kolla-mergepwd \
  --old   custom-config/wrx/passwords.yml.old \
  --new   custom-config/wrx/passwords.yml     \
  --final custom-config/wrx/passwords.yml

# Review the differences
diff -y --suppress-common-lines  custom-config/wrx/passwords.yml custom-config/wrx/passwords.yml.old
```

---
# Roll out the Next Release
## Go
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# 💾 Create config backups                                                  📋
ansible  all -b -m ansible.builtin.shell -a \
  "mkdir -p ~/backup; cp  -r /etc/kolla ~/backup/$(date +%y%m%d%H%M%S)_kolla" 

# 🗂️ Force to collect facts
kolla-ansible gather-facts -i inventory/wrx -e "ansible_python_interpreter=/usr/bin/python3"

# ansible all -m ansible.builtin.setup

# 🩺 Check
kolla-ansible prechecks    -i inventory/wrx

# 📥 Pull images
kolla-ansible pull         -i inventory/wrx

# 🚚 Upgrade
kolla-ansible upgrade      -i inventory/wrx
```
---
# High Availability
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
* Keepalived & HAProxy (controllers)
* Stateless API Services
* MariaDB Galera Cluster as State Store
* Message Broker (RabbitMQ)
* Multiple nodes of each type

---
# Debugging Common
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Some useful commands for debugging                                        📋

# On each node
docker ps  | grep unhealthy
df -Ph

# On controller node
tail -f /var/log/kolla/mariadb/mariadb.log
tail -f /var/log/kolla/rabbitmq/rabbit*.log

# On deployment node
openstack compute service list
openstack volume  service list
openstack network agent   list

# Check HA IP on controller nodes
ip -br a

# Search for error messages connected with a resource
grep -r [RESOURCE_ID] /var/log/kolla/[SERVICE]
```

---
# Backup and Recovery
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
* MySQL   Backup
* Config  Backup (can be restored by kolla)
* Volumes Backup (can be ⚠️ partially restored)
* Message Queues (are recreated if lost)

---
# Database Backup and Recovery
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Create a new backup                                                       📋
kolla-ansible mariadb-backup -i inventory/wrx

# Check on the controller
ls -lh /var/lib/docker/volumes/mariadb_backup/_data/

# To restore
gunzip -c <BACKUP_FILE> | docker exec -i mariadb mysql -u root -p

# If the DB cluster cannot create a quorum
kolla-ansible mariadb-recovery -i inventory/wrx
```
---
# Monitoring
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
<style scoped>
table, thead, tbody, tr, th, td {
  background-color: transparent !important;
  border: none !important;
}
</style>
* Ceilometer / Gnocchi / Aodh (for Billing)
* Prometheus / Alertmanager / Grafana

| | |
| :--- | :--- |
| Grafana      | [http://int.os.wrx.sckt.net:3000](http://int.os.wrx.sckt.net:3000) |
| Prometheus   | [http://int.os.wrx.sckt.net:9091](http://int.os.wrx.sckt.net:9091) |
| Alertmanager | [http://int.os.wrx.sckt.net:9095](http://int.os.wrx.sckt.net:9091) |

---
# Expand / Deploy a New Service
# Central Logging
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# 🔌 Enable central logging                                                 📋
cat custom-config/wrx/globals.d/logging.yml
sed -e 's/enable_central_logging:.*/enable_central_logging: "yes"/' \
    -i custom-config/wrx/globals.d/logging.yml
cat custom-config/wrx/globals.d/logging.yml

# 🚚 Deploy the new service
kolla-ansible pull          -i inventory/wrx --tags opensearch
kolla-ansible deploy        -i inventory/wrx --tags opensearch
kolla-ansible reconfigure   -i inventory/wrx --tags common
```

Visit: 
- http://int.os.wrx.sckt.net:5601
- visualize, create an index pattern
- discover


---
# Using OpenStack
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)


---
# Images
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Prepare images                                                            📋
mkdir -p  ~/cloud-images/

ALMA_VERSION="10.2"
ALMA_IMG_NAME=$(
  curl -sS https://repo.almalinux.org/almalinux/10/cloud/x86_64/images/CHECKSUM  \
   | grep GenericCloud-$ALMA_VERSION | awk '{print $2}' |  sort  | tail -n1)
ALMA_LINK=https://repo.almalinux.org/almalinux/10/cloud/x86_64/images/$ALMA_IMG_NAME

ROCKY_VERSION="10.2"
ROCKY_IMG_NAME=$(
  curl -sS https://dl.rockylinux.org/pub/rocky/10/images/x86_64/CHECKSUM \
  | grep SHA256 | awk '{print $2}'  | grep GenericCloud-LVM-$ROCKY_VERSION \
  | sed -e "s/[()]//g" | sort | tail -n1)
ROCKY_LINK=https://dl.rockylinux.org/pub/rocky/10/images/x86_64/$ROCKY_IMG_NAME

UBUNTU_VERSION="24.04"
UBUNTU_IMG_NAME=noble-server-cloudimg-amd64.img
UBUNTU_LINK="https://cloud-images.ubuntu.com/noble/current/$UBUNTU_IMG_NAME"

DEBIAN_VERSION="13"
DEBIAN_IMG_NAME=debian-13-generic-amd64.qcow2
DEBIAN_LINK="https://cloud.debian.org/images/cloud/trixie/latest/$DEBIAN_IMG_NAME"
```

---
# Images
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Download images                                                           📋
for i in  alma~$ALMA_VERSION~$ALMA_LINK       \
          rocky~$ROCKY_VERSION~$ROCKY_LINK    \
          ubuntu~$UBUNTU_VERSION~$UBUNTU_LINK \
          debian~$DEBIAN_VERSION~$DEBIAN_LINK
do
  IFS="~" read -r -a fields <<< $i
  name=${fields[0]}
  version=${fields[1]}
  link=${fields[2]}
  filepath=~/cloud-images/$(basename $link)

  echo "Downloading $name-$version image"
  curl --output-dir ~/cloud-images -L -O $link

  echo "Uploading $name-$version image into OpenStack"
  openstack image create "$name-$version"       \
      --file             "$filepath"            \
      --disk-format       qcow2                 \
      --container-format  bare                  \
      --property          os_distro="$name"     \
      --property          os_version="$version" \
      --public
done

# Check
openstack image list --long
```

---
# Authentication / Authorization
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Set permissions                                                           📋
PRJ_NAME=workshop
USR_NAME=workshop
USR_PASS=workshop

# Create project
openstack project create                             $PRJ_NAME
openstack quota set --instances 32 --cores 64        $PRJ_NAME

# Create user
openstack user create --password $USR_PASS --project $PRJ_NAME $USR_NAME

# Create permissions for the user
openstack role add    --user $USR_NAME     --project $PRJ_NAME member

# Check
openstack role assignment list --name
openstack quota show $PRJ_NAME
```

---
# How to authenticate with CLI
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Prepare an openrc file to be used with the OpenStack CLI                  📋
cp  custom-config/wrx/admin-openrc.sh  custom-config/wrx/workshop-openrc.sh

sed  -e "s/OS_PROJECT_NAME=.*/OS_PROJECT_NAME=$PRJ_NAME/" -i custom-config/wrx/workshop-openrc.sh
sed  -e "s/OS_TENANT_NAME=.*/OS_TENANT_NAME=$PRJ_NAME/"   -i custom-config/wrx/workshop-openrc.sh
sed  -e "s/OS_PASSWORD=.*/OS_PASSWORD=$USR_PASS/"         -i custom-config/wrx/workshop-openrc.sh
sed  -e "s/OS_USERNAME=.*/OS_USERNAME=$USR_NAME/"         -i custom-config/wrx/workshop-openrc.sh

# Review
cat    custom-config/wrx/workshop-openrc.sh
# Activate
source custom-config/wrx/workshop-openrc.sh

openstack network list
openstack router  list
openstack server  list
```
---
# Networking
![bg right:40% 90%](assets/openstack/bootstrap-test-environment.svg)
* Networks:
  * flat
  * vlan
  * vxlan
* Routers
* Floating IPs
* Load Balancers

---
# Networking
## **External Network**
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)

```bash
# Create external network (VLAN backed)                                     📋
# as admin
EXT_NET_VLAN=3010
EXT_NET_NAME="shared${EXT_NET_VLAN}"
EXT_NET_CIDR='10.30.10.0/24'
EXT_NET_RANGE='start=10.30.10.192,end=10.30.10.254'
EXT_NET_GATEWAY='10.30.10.1'

openstack network create       ${EXT_NET_NAME}  \
   --provider-physical-network physnet3         \
   --provider-network-type     vlan             \
   --provider-segment          ${EXT_NET_VLAN}  \
   --external

openstack subnet create  ${EXT_NET_NAME}-subnet \
    --allocation-pool    ${EXT_NET_RANGE}       \
    --network            ${EXT_NET_NAME}        \
    --subnet-range       ${EXT_NET_CIDR}        \
    --gateway            ${EXT_NET_GATEWAY}     \
    --no-dhcp
```

---
# Networking
## **Project Network**
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Create project network (VXLAN backed)                                     📋
# as workshop user in workshop project

# Activate
source custom-config/wrx/workshop-openrc.sh

PRJ_NAME=workshop
openstack network create workshop-net

openstack subnet create workshop-subnet \
  --subnet-range 10.10.10.0/24          \
  --network workshop-net                \
  --gateway 10.10.10.1

openstack router create workshop-router
openstack router add subnet workshop-router workshop-subnet
openstack router set --external-gateway shared3010 workshop-router
```

---
# Networking
##  **Virtual Machines**
![bg right:50% 30%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# as workshop user in workshop project

IMAGE_NAME=ubuntu-24.04
# IMAGE_NAME=alma-10.1
# IMAGE_NAME=rocky-10.1
# IMAGE_NAME=debian-13

openstack keypair create --public-key ~/.ssh/id_ecdsa.pub mykey

openstack server create          \
  --image    ${IMAGE_NAME}       \
  --flavor   m1.small            \
  --key-name mykey               \
  --nic      net-id=workshop-net \
  vm0

openstack server create                  \
  --image            ${IMAGE_NAME}       \
  --flavor           m1.small            \
  --key-name         mykey               \
  --nic              net-id=workshop-net \
  --boot-from-volume 10                  \
  vm1
```


---
# Networking
## **How to access?**
![bg right:40% 90%](../assets/marp/openstack/networking-how-to-access.svg)

```bash
# Create a config for a VLAN interface                                      📋
# on the testing node as root
myid=$(cat /etc/env.json  | jq .id)

cat > /etc/netplan/80-vlan.yaml <<EOF
network:
  version: 2
  vlans:
    xtrn0.3010:
      addresses:
      - "10.30.10.${myid}/24"
      dhcp4: false
      dhcp6: false
      id: 3010
      link: eth3
      link-local: []
EOF

chmod 600 /etc/netplan/80-vlan.yaml

netplan apply
```

---
# Networking
## **How to access?**
![bg right:50% 30%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Copy the ssh key from deployment to testing node                          📋
scp ~/.ssh/id_ecdsa* testing.mgmt.wrx.sckt.net:~/.ssh/

# Assign floating IPs to VMs
openstack floating ip  create shared3010
FIP_VM0=$(
  openstack floating ip  create shared3010 -f value -c floating_ip_address
)
openstack server add floating ip vm0 $FIP_VM0

FIP_VM1=$(
  openstack floating ip  create shared3010 -f value -c floating_ip_address
)
openstack server add floating ip vm1 $FIP_VM1

# Review assigned IPs
openstack server list

# Allow access to vm0
openstack security group create allow-simple-access
openstack security group rule create allow-simple-access \
  --protocol tcp \
  --dst-port 22 \
  --remote-ip 0.0.0.0/0 \
  --ingress
openstack security group rule create allow-simple-access   \
  --protocol icmp \
  --remote-ip 0.0.0.0/0 \
  --ingress
openstack server add security group vm0 allow-simple-access

# You should be able to connect to VMs from the testing node
ping -c 3 <IP>
ssh -i ~/.ssh/id_ecdsa ubuntu@<IP>
```


---
# Networking
## **Debugging**
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Check VMs and IPs assigned to them                                        📋
openstack server list --long

# Check the state of network services
openstack network agent list

# Check the router has connectivity to the external network
# Check the router has IPs from the tenant network assigned
openstack router show workshop-router

# On network nodes check if there are network namespaces created for the router
# qrouter-<ROUTER_ID>
# snat-<ROUTER_ID>
# fip-<FLOATING_IP_NETWORK_ID>
ip netns
# Check if IP addresses are assigned
ip netns exec <NETWORK_NAMESPACE_ID> ip a

# On compute nodes check if there are network namespaces created for the router
ip netns
```

---
# Network Namespaces
![bg right:50% 90%](https://www.funtoo.org/images/thumb/c/ce/2host-4vm.png/733px-2host-4vm.png)

---
# User Config with Cloud-Init
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Create user-data.yaml                                                     📋
cat > user-data.yaml <<EOF
#cloud-config
runcmd:
  - mkdir -p /var/lib/data/test
  - echo "<h1>Hello from write_files</h1>" > /var/lib/data/test/hello.html
fs_setup:
  - device:     /dev/vdb
    filesystem: ext4
    overwrite:  true
mounts:
  - [ /dev/vdb, /var/lib/data, "ext4", "defaults,nofail", "0", "2" ]
write_files:
  - path:        /var/lib/data/index.html
    owner:       root:root
    permissions: '0644'
    content: |
      <h1>Hello from write_files</h1>
EOF
```

---
# User Config with Cloud-Init
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Create a new VM with an additional volume                                 📋
root_uuid=$(
  openstack volume create \
    --image "${IMAGE_NAME}" --size 10 -c Id -f value vm2-root
)
data_uuid=$(openstack volume create --size 10 -c Id -f value vm2-data )

openstack volume list

openstack server create \
  --flavor m1.small \
  --key-name mykey \
  --nic net-id=workshop-net \
  --block-device uuid=$root_uuid,source_type=volume,destination_type=volume,boot_index=0 \
  --block-device uuid=$data_uuid,source_type=volume,destination_type=volume,boot_index=-1 \
  --user-data ./user-data.yaml \
  vm2


# Inspect
openstack server show vm2 -fyaml
openstack volume list

# Create a new floating IP and attach it to the server
vm2_ip=$(openstack floating ip create -f value -c floating_ip_address shared3010)
openstack server add floating ip vm2 $vm2_ip
```
---
# User Config - Inspect the Created VM
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Log in from testing node                                                  📋
ssh -i ~/.ssh/id_ecdsa ubuntu@<FLOATING_IP>

cat /etc/fstab
mount | grep vdb

cat /var/lib/data/index.html
cat /var/lib/data/test/hello.html
```

---
# Debugging
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Inspect the instance                                                      📋
openstack server show vm2

# Inspect VM with libvirt on the compute host
docker exec -ti nova_libvirt virsh list
docker exec -ti nova_libvirt virsh dumpxml    <INSTANCE_NAME>
docker exec -ti nova_libvirt virsh console    <INSTANCE_NAME> # ctrl+5 to exit
docker exec -ti nova_libvirt virsh domblklist <INSTANCE_NAME>
docker exec -ti nova_libvirt virsh domiflist  <INSTANCE_NAME>

# Inspect volumes backed by Ceph on the compute host
docker exec -ti nova_compute ls /etc/ceph/
docker exec -ti nova_compute ceph -s
docker exec -ti nova_compute ceph --id cinder -s
docker exec -ti nova_compute ceph --id cinder osd pool ls
docker exec -ti nova_compute rbd  --id cinder ls images
docker exec -ti nova_compute rbd  --id cinder ls vms
docker exec -ti nova_compute rbd  --id cinder ls volumes

# Inspect network interface on the compute host
docker exec -ti openvswitch_vswitchd ovs-vsctl show

# Within the instance
cloud-init status
cloud-init status --long
```

---
# Block Devices
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# How many VMs are running? How many volumes should we see?                 📋
openstack volume list

# Create a new volume and attach it
openstack volume create  --size 1 vm1-data
openstack server add volume   vm1 vm1-data
openstack volume attachment list

# Inspect it within the VM from the testing node
lsblk

# Create some data on it
mkfs.ext4 /dev/vdb
echo "$(date) Hello from vm1"

# Take a snapshot on the deployment node
openstack volume snapshot create --volume vm1-data vm1-data-snap1 [--force]
openstack volume snapshot list

# Change the data
echo "Let us add a new line into the file after the snapshot was created"  >> /mnt/test
cat  /mnt/test
```
---
# Block Devices
## **Revert a Volume**
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Restore from the snapshot                                                 📋
openstack server stop   vm1
openstack server list;  openstack volume list;
openstack volume snapshot list --volume vm1-data

openstack server remove volume vm1 vm1-data
openstack volume revert            vm1-data-snap1
openstack server add    volume vm1 vm1-data
openstack server start         vm1
```

---
# Block Devices
## **More Operations**
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
# Create a volume from an image                                             📋
openstack volume create --image cirros --size 1 vm1-cirros

# Create a volume from a snapshot
openstack volume snapshot create --volume vm2-root vm2-root-snap1 [--force]
openstack volume create --snapshot vm2-root-snap1 ubuntu-24.04-from-vm2

# Create an image from a volume
openstack image create  --volume ubuntu-24.04-from-vm2 ubuntu-24.04-custom 
openstack image list 
```

---
# Resource Segregation
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
- Projects
- Host Aggregates
- Volume Backends
- Network Agents
- Availability Zones

---
# Availability Zones
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
is a human-readable label that represents a **physically isolated slice of data center infrastructure**. It is the primary tool used to group hardware into separate **fault domains** so that a single physical failure does not take down an entire cloud application.

---
# Availability Zones
![bg right:30% 50%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
* Power Distribution Unit
* Data Center (Room)
* Cooling Domain
* Top-of-Rack (ToR) Switch Domain
* Storage Backend

---
# Availability Zones
![bg right:50% 30%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
openstack aggregate create --zone az1 prd-az1
openstack aggregate create --zone az2 prd-az2

openstack aggregate add host prd-az1 compute01
openstack aggregate add host prd-az1 compute02
openstack aggregate add host prd-az1 compute03

openstack aggregate show prd-az1
openstack aggregate show prd-az2

openstack availability zone list --compute
openstack availability zone list --network
openstack availability zone list --volume
```

---
# Host Aggregates
![bg right:50% 30%](https://www.svgrepo.com/show/354145/openstack-icon.svg)
```bash
openstack aggregate create hw-type-A
openstack aggregate create hw-type-B
openstack aggregate create windows
openstack aggregate create gpu

openstack aggregate add host hw-type-A compute01
openstack aggregate add host hw-type-A compute02
openstack aggregate add host hw-type-A compute03
openstack aggregate add host hw-type-B compute11

openstack aggregate add host windows compute01
openstack aggregate add host windows compute11

openstack aggregate add host gpu compute11

openstack aggregate show windows
```

---
# Host Aggregates vs. Availability Zones
![bg right:50% 90%](https://web-assets.dd-static.net/42588/1776303838-openstack-host-aggregates-flavors-availability-zones-aggregates1.png?format=auto&fit=bounds&quality=75&disable=upscale&width=1026&dpr=1)

---
# Links
- [OpenStack - Docs](https://docs.openstack.org)
- [OpenStack - Configuration Guides](https://docs.openstack.org/latest/configuration/)
- [OpenStack - Releases](https://releases.openstack.org/)
<br/>
- [OpenStack - Horizon / Dashboard](http://int.os.wrx.sckt.net)
- [OpenStack - Skyline / Dashboard](http://int.os.wrx.sckt.net:9999)
- [OpenStack - Grafana / Monitoring](http://int.os.wrx.sckt.net:3000)
- [OpenStack - Prometheus / Monitoring](http://int.os.wrx.sckt.net:9091)
- [OpenStack - Alertmanager](http://int.os.wrx.sckt.net:9093)

---
# Links
- [Cloud-Init - Docs](https://docs.cloud-init.io/en/latest/reference/examples.html)
<br/>
- [Harbor - Registry](http://registry.wrx.sckt.net/)

---

# Feedback and contributions

Found an error or want to contribute? Open a PR on [GitHub](https://github.com/codecap/infraguide/tree/main).

More labs and cheat sheets: [infraguide.org](https://infraguide.org)

Questions or ideas: ping@infraguide.org
