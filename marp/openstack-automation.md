---
title:       Automation
description: OpenStack & Ceph
author:      Vladislav Nazarenko (vnazarenko@📯socket.de)
keywords:    openstack,ceph,workshops,operations,troubleshoouting
url:
image:
backgroundImage: url(https://raw.githubusercontent.com/codecap/infraguide/refs/heads/main/assets/marp/background.jpg)
transition: cover
paginate: true
footer: "[infraguide.org](https://infraguide.org) | Questions or need a guided workshop? → ping@infraguide.org"
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

# Automation

---
# Ansible
![bg right:30% 50%](https://www.svgrepo.com/show/373429/ansible.svg)

```bash
# On testing node                                                           📋
python3 -m venv ~/venv/ansible
source  ~/venv/ansible/bin/activate

# Clone openstack-workshop git repository
git clone -c http.proxy="http://proxy.wrx.sckt.net:3128"  \
          -c https.proxy="http://proxy.wrx.sckt.net:3128" \
          https://github.com/codecap/openstack-workshop.git

# Install dependencies for Ansible
cd  openstack-workshop/automation/ansible
pip install --proxy=http://proxy.wrx.sckt.net:3128 -U pip
pip install --proxy=http://proxy.wrx.sckt.net:3128 -r requirements.txt

# Install dependencies via ansible-galaxy
http_proxy=http://proxy.wrx.sckt.net:3128  \
https_proxy=http://proxy.wrx.sckt.net:3128 \
ansible-galaxy collection install -r requirements.yml --force
```

---
# Credentials
![bg right:30% 50%](https://www.svgrepo.com/show/529035/key-minimalistic-square-3.svg)
```bash
cat > ~/automation-openrc.sh<<"EOF"

# Clear any old environment that may conflict.
for key in $( set | awk '{FS="="}  /^OS_/ {print $1}' ); do unset $key ; done

export OS_ENDPOINT_TYPE=internalURL
export OS_REGION_NAME=RegionOne
export OS_INTERFACE=internal
export OS_AUTH_PLUGIN=password
export OS_AUTH_URL=http://int.os.wrx.sckt.net:5000
export OS_PROJECT_DOMAIN_NAME=Default
export OS_TENANT_NAME=automation
export OS_USERNAME=automation
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=automation
export OS_PASSWORD=automation
export OS_IDENTITY_API_VERSION=3
EOF
```
---
# Credentials
![bg right:30% 50%](https://www.svgrepo.com/show/529035/key-minimalistic-square-3.svg)
```bash
cat > clouds.yml <<EOF
---
clouds:
  wrx:
    auth:
      auth_url:            "http://int.os.wrx.sckt.net:5000/v3"
      username:            "automation"
      password:            "automation"
      project_name:        "automation"
      user_domain_name:    "Default"
      project_domain_name: "Default"
    region_name: "RegionOne"
    interface: "internal"
    identity_api_version: 3
EOF

```

---
# Run
![bg right:50% 30%](https://www.svgrepo.com/show/373429/ansible.svg)
```bash
# Create automation project
source ~/admin-openrc.sh 
ansible-playbook playbooks/create-project.yml

# Create test infra in automation project
source ~/automation-openrc.sh 
ansible-playbook playbooks/create-infra.yml

# Destroy test infra
ansible-playbook playbooks/destroy-infra.yml
```



---
# Terraform
![bg right:50% 30%](https://www.svgrepo.com/show/354447/terraform-icon.svg)

```bash
# Install Terraform on testing node                                         📋
KEYRING_PATH=/usr/share/keyrings/hashicorp-archive-keyring.gpg

curl -fsSL https://apt.releases.hashicorp.com/gpg \
  --proxy http://proxy.wrx.sckt.net:3128          \
  | sudo gpg --dearmor -o $KEYRING_PATH

echo "deb [signed-by=$KEYRING_PATH] https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
  | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt-get update && sudo apt-get install terraform

cd ../terraform
terraform --version

# Download providers
http_proxy=http://proxy.wrx.sckt.net:3128  \
https_proxy=http://proxy.wrx.sckt.net:3128 \
terraform init

ls -la
cat .terraform.lock.hcl
terraform providers 

# Run
TF_VAR_os_password=$OS_PASSWORD terraform plan
TF_VAR_os_password=$OS_PASSWORD terraform apply -auto-approve
```



---
# Testinfra
![bg right:50% 30%](https://testinfra.readthedocs.io/en/latest/_static/logo.svg)

```bash
cd ../testinfra/
py.test  --hosts=ssh://ubuntu@<SERVER_IP> server.py -v
```
