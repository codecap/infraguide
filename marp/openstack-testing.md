---
title:       Testing
description: OpenStack & Ceph
author:      Vladislav Nazarenko (vnazarenko@📯socket.de)
keywords:    openstack,ceph,workshops,operations,troubleshoouting
url:         
image:
transition: cover
backgroundImage: url(https://raw.githubusercontent.com/codecap/infraguide/refs/heads/main/assets/marp/background.jpg)
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
# Testing
--- 
# Why?
- Changes
- Regression
- Impact of outside events (Network, Storage)
- Monitoring (over time)
- Confidence (I have a working environment)
- Recognition of potential problems (in advance)
---
# Challenges
- My specific configuration
- My specific implementation / deployment / infra
- Networking
- Changes outside of my environment (BGP, Storage, Object Storage, DNS)
---
# Tempest
## **Install**
![bg right:50% 30%](https://www.openstack.org/software/images/mascots/tempest.png)
[Release Notes Tempest v45](https://docs.openstack.org/releasenotes/tempest/v45.0.0.html)
```bash
# Install Tempest on testing node                                           📋
sudo apt-get update
sudo apt-get install build-essential python3.12-dev -y
python3 -m venv ~/venv/tempest
echo ""                                   >> ~/.profile
echo "source ~/venv/tempest/bin/activate" >> ~/.profile
source ~/.profile
pip install -U pip --proxy http://proxy.wrx.sckt.net:3128
pip install --proxy http://proxy.wrx.sckt.net:3128 -r \
  $WRX_RAW_BASE_PATH//testing/tempest/requirements.txt
```
---
# Tempest
## **Configuration**
![bg right:50% 30%](https://www.openstack.org/software/images/mascots/tempest.png)
```bash
# Copy admin-openrc.sh file from deployment to testing node                 📋
scp ~/openstack/custom-config/wrx/admin-openrc.sh testing.mgmt:~/
# On testing node source your openrc (credentials) file
source ~/admin-openrc.sh
# The configuration script
CONF_SCRIPT=$WRX_RAW_BASE_PATH/testing/tempest/configure.sh
# Review
curl --proxy http://proxy.wrx.sckt.net:3128 -L $CONF_SCRIPT
# Run
curl --proxy http://proxy.wrx.sckt.net:3128 -L $CONF_SCRIPT -sS | bash
# Review generated config
cd  ~/tempest-45
cat etc/tempest.conf
# Create a list of available tests
stestr list > tests-available.list
# Create a list of smoke tests
cat tests-available.list | grep smoke > smoke-tests.list
# How to run
tempest run --load-list smoke-tests.list  --regex \
  tempest.api.network.test_security_groups.SecGroupTest.test_list_security_groups
```
---
# Scripts
[openstack-health-monitor](https://github.com/SovereignCloudStack/openstack-health-monitor)
```bash
mkdir testing
git clone https://github.com/SovereignCloudStack/openstack-health-monitor.git
export IMG=ubuntu-24.04
export JHIMG=$IMG
export FLAVOR=m1.small
export JHFLAVOR=$FLAVOR
export ADDJHVOLSIZE=10
source ~/venv/ansible/bin/activate
source ~/automation-openrc.sh
```
---
# Run
```bash
# -o    - translate nova/cinder/neutron/glance into OpenStack client commands
# -C    - full connectivity check: every VM pings every other
# -D    - create all VMs with one API call (implies -d -P)
# -n 1  - number of VMs to create
# -B    - measure TCP bandwidth between VMs (iperf3)
# -T    - assign tags to resources; use to clean up floating IPs
# -s 10 - send stats once per day (or every SH hours), not just alarms
# -W 45 - set error wait (VM only): 0-inf seconds or negative value for interactive wait
./api_monitor.sh -o -C -D -n 1 -B -T -s 10 -W 45
```
