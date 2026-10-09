---
layout: default
title: "OpenStack CLI Cheat Sheet: Common Commands | infraguide.org"
description: "OpenStack CLI cheat sheet: commands for users, projects, flavors, images, networks, volumes, instances and floating IPs. Copy and paste ready."
breadcrumbs:
  - name: cheat-sheets
    url: /cheat-sheets/
  - name: openstack
---

# OpenStack CLI Cheat Sheet

Common openstack commands, grouped by service. Replace the values in angle brackets.

{% include cheat-sheet-notation.md %}

Want to see these commands in context? Build the [OpenStack lab](/learn/openstack/).

## Services
```bash
# list OpenStack services
openstack catalog list
```

## Domains
```bash
# list domains
openstack domain list
# show domain details
openstack domain show <DOMAIN_ID>
# create domain
openstack domain create <DOMAIN_NAME>
# update domain
openstack domain set <KEY> <VALUE> <DOMAIN_ID>
# delete domain
openstack domain delete <DOMAIN_ID>
```

## Users
```bash
# list users
openstack user list
# show user details
openstack user show <USER_ID>
# create user
openstack user create --password <PASSWORD> <USER_NAME>
# update user
openstack user set <KEY> <VALUE> <USER_ID>
# set user password
openstack user password set
# delete user
openstack user delete <USER_ID>
```

## Groups
```bash
# list groups
openstack group list
# show group details
openstack group show <GROUP_ID>
# create group
openstack group create <GROUP_NAME>
# update group
openstack group set <KEY> <VALUE> <GROUP_ID>
# add user to group
openstack group add user <GROUP_ID> <USER_ID>
# remove user from group
openstack group remove user <GROUP_ID> <USER_ID>
# delete group
openstack group delete <GROUP_ID>
```

## Projects
```bash
# list projects
openstack project list
# create project
openstack project create <PROJECT_NAME>
# update project
openstack project set <KEY> <VALUE> <PROJECT_ID>
# delete project
openstack project delete <PROJECT_ID>
```

## Flavors
```bash
# list flavors
openstack flavor list
# show flavor details
openstack flavor show <FLAVOR_NAME>
# create flavor
openstack flavor create --vcpus <VCPUS> --ram <RAM_MB> \
  --disk <DISK_GB> <FLAVOR_NAME>
# update flavor
openstack flavor set <KEY> <VALUE> <FLAVOR_NAME>
# delete flavor
openstack flavor delete <FLAVOR_NAME>
```

## Roles
```bash
# assign role on project
openstack role add --project <PROJECT_ID> \
  {--user <USER_ID>|--group <GROUP_ID>} <ROLE_NAME>
# remove role on project
openstack role remove --project <PROJECT_ID> \
  {--user <USER_ID>|--group <GROUP_ID>} <ROLE_NAME>
```

## Images
```bash
# list images
openstack image list
# show image details
openstack image show <IMAGE_ID>
# create image
openstack image create --disk-format qcow2 \
  --file <FILE_PATH> <IMAGE_NAME>
# update image
openstack image set <KEY> <VALUE> <IMAGE_ID>
# delete image
openstack image delete <IMAGE_ID>
```

## Networks
```bash
# list networks
openstack network list
# show network details
openstack network show <NETWORK_ID>
# create network
openstack network create <NETWORK_NAME>
# update network
openstack network set <KEY> <VALUE> <NETWORK_ID>
# delete network
openstack network delete <NETWORK_ID>
```

## Subnets
```bash
# list subnets
openstack subnet list
# show subnet details
openstack subnet show <SUBNET_ID>
# create subnet
openstack subnet create --network <NETWORK_ID> \
  --subnet-range <SUBNET_CIDR> <SUBNET_NAME>
# update subnet
openstack subnet set <KEY> <VALUE> <SUBNET_ID>
# delete subnet
openstack subnet delete <SUBNET_ID>
```

## Security groups
```bash
# list security groups
openstack security group list
# show security group details
openstack security group show <SECURITY_GROUP_ID>
# create security group
openstack security group create <SECURITY_GROUP_NAME>
# update security group
openstack security group set <KEY> <VALUE> <SECURITY_GROUP_ID>
# list rules in the security group
openstack security group rule list <SECURITY_GROUP_ID>
# add rule to the security group
openstack security group rule create <KEY> <VALUE> ... <SECURITY_GROUP_ID>
# delete rule from the security group
openstack security group rule delete <RULE_ID>
# delete security group
openstack security group delete <SECURITY_GROUP_ID>
```

## Routers
```bash
# list routers
openstack router list
# show router details
openstack router show <ROUTER_ID>
# create router
openstack router create <ROUTER_NAME>
# update router
openstack router set <KEY> <VALUE> <ROUTER_ID>
# attach subnet to router
openstack router add subnet <ROUTER_ID> <SUBNET_ID>
# detach subnet from router
openstack router remove subnet <ROUTER_ID> <SUBNET_ID>
# delete router
openstack router delete <ROUTER_ID>
```

## Key pairs
```bash
# list key pairs
openstack keypair list
# show key pair details
openstack keypair show <KEY_PAIR_NAME>
# create key pair
openstack keypair create --private-key <FILE_PATH> <KEY_PAIR_NAME>
# delete key pair
openstack keypair delete <KEY_PAIR_NAME>
```

## Quotas
```bash
# list default quotas
openstack quota show --default
# update default quotas
openstack quota set <KEY> <VALUE> --class default
# list project quotas
openstack quota show <PROJECT_ID>
# update project quotas
openstack quota set <KEY> <VALUE> <PROJECT_ID>
```

## Volumes
```bash
# list volumes
openstack volume list
# show volume details
openstack volume show <VOLUME_ID>
# create volume
openstack volume create --size <SIZE_GB> <VOLUME_NAME>
# update volume
openstack volume set <KEY> <VALUE> <VOLUME_ID>
# delete volume
openstack volume delete <VOLUME_ID>
```

## Instances
```bash
# list instances
openstack server list
# show instance details
openstack server show <INSTANCE_ID>
# create instance
openstack server create --flavor <FLAVOR_NAME> \
  --image <IMAGE_ID> --network <NETWORK_ID> \
  --key-name <KEY_PAIR_NAME> <INSTANCE_NAME>
# update instance
openstack server set <KEY> <VALUE> <INSTANCE_ID>
# attach volume to instance
openstack server add volume <INSTANCE_ID> <VOLUME_ID>
# detach volume from instance
openstack server remove volume <INSTANCE_ID> <VOLUME_ID>
# delete instance
openstack server delete <INSTANCE_ID>
```

## Floating IPs
```bash
# list floating IPs
openstack floating ip list
# create floating IP
openstack floating ip create <NETWORK_ID>
# attach floating IP to instance
openstack server add floating ip <INSTANCE_ID> <FLOATING_IP_ID>
# detach floating IP from instance
openstack server remove floating ip <INSTANCE_ID> <FLOATING_IP_ID>
# delete floating IP
openstack floating ip delete <FLOATING_IP_ID>
```

## Permissions: users, groups, roles
```bash
# User with its own domain and default project
openstack user create --domain <DOMAIN> --project <PROJECT> \
  --description "<DESCRIPTION>" --email <EMAIL> --password <PASSWORD> \
  --enable <USER>

# Role in a domain
openstack role create --domain Default <ROLE>

# Group in the default domain, add and check a member
openstack group create --domain Default --description "<DESCRIPTION>" <GROUP>
openstack group add user <GROUP> <USER>
openstack group contains user <GROUP> <USER>

# Group in another domain, members come from that domain too
openstack group create --domain <DOMAIN> --description "<DESCRIPTION>" <GROUP>
openstack group add user      --group-domain <DOMAIN> <GROUP> <USER>
openstack group contains user --group-domain <DOMAIN> <GROUP> <USER>

# Tokens
openstack token issue
openstack token issue -f yaml
openstack token revoke <TOKEN>
```

## Catalog & services
```bash
# Register a service and its endpoints
openstack service create --name <NAME> --description "<DESCRIPTION>" <TYPE>
openstack endpoint create --region <REGION> <NAME> public   http://<HOST>:<PORT>
openstack endpoint create --region <REGION> <NAME> internal http://<HOST>:<PORT>
openstack endpoint create --region <REGION> <NAME> admin    http://<HOST>:<PORT>

# Service status
openstack service list --long
openstack network agent list --long
openstack compute service list --long
openstack volume service list --long

# Take a compute host out of scheduling
openstack compute service set --disable <HOST> nova-compute
```

## Images: examples
```bash
openstack image create --file <IMAGE_FILE> --private \
  --property description="<DESCRIPTION>" <IMAGE_NAME>
openstack image set --min-ram <MB> <IMAGE_NAME>
openstack image set --property os_shutdown_timeout=<SECONDS> <IMAGE_NAME>
openstack image show <IMAGE_NAME>

# Octavia amphora image, the release is taken from the kolla-ansible config
os_release=$(
  cat $VIRTUAL_ENV/share/kolla-ansible/ansible/group_vars/all.yml \
    | y2j \
    | jq .openstack_release -Mr
)
amphora_image_file=octavia-amphora-haproxy-${os_release}.qcow2
amphora_image_url="https://<IMAGE_HOST>/$amphora_image_file"
mkdir -p ~/cloud-images/
curl -o ~/cloud-images/$amphora_image_file $amphora_image_url

openstack image create amphora-x64-haproxy \
  --container-format bare --disk-format qcow2 --private --tag amphora \
  --file ~/cloud-images/$amphora_image_file \
  --property hw_architecture='x86_64' --property hw_rng_model=virtio
```

## Baremetal flavor
```bash
# Schedule on the custom resource class of the node, not on VCPU / RAM / disk
openstack flavor create --ram <RAM_MB> --disk <DISK_GB> --vcpus <VCPUS> <FLAVOR_NAME>
openstack flavor set <FLAVOR_NAME> \
  --property resources:CUSTOM_<RESOURCE_CLASS>=1 \
  --property resources:VCPU=0 \
  --property resources:MEMORY_MB=0 \
  --property resources:DISK_GB=0
```

## Provider network setup
```bash
# VLAN provider network, subnet and router with an external gateway
openstack network create <NETWORK_NAME> \
  --provider-network-type vlan \
  --provider-physical-network <PHYSNET> \
  --project <PROJECT>

openstack subnet create <SUBNET_NAME> \
  --subnet-range <CIDR> \
  --gateway <GATEWAY_IP> \
  --network <NETWORK_NAME> \
  --project <PROJECT>

openstack router create <ROUTER_NAME> --project <PROJECT>
openstack router add subnet <ROUTER_NAME> <SUBNET_NAME>
openstack router set --external-gateway <EXT_NETWORK> <ROUTER_NAME>

# Floating IP from the external network, attach it to an instance
openstack floating ip create <EXT_NETWORK>
openstack server add floating ip <SERVER> <FLOATING_IP>
```

## Placement
```bash
# --- Resource providers ---
openstack resource provider list
openstack resource provider show <UUID>
openstack resource provider create <NAME>
openstack resource provider delete <UUID>

# --- Inventory ---
openstack resource provider inventory list <UUID>
openstack resource provider inventory show <UUID> <RESOURCE_CLASS>
openstack resource provider inventory set <UUID> <RESOURCE_CLASS> \
  --total <N> --reserved <N> --min-unit <N> --max-unit <N> --step-size <N>
openstack resource provider inventory delete <UUID> <RESOURCE_CLASS>

# --- Aggregates ---
openstack resource provider aggregate list <UUID>
openstack resource provider aggregate set <UUID> --aggregate <AGG_UUID>

# --- Traits ---
openstack resource provider trait list <UUID>
openstack resource provider trait set <UUID> --trait <TRAIT_NAME>
openstack resource provider trait delete <UUID>
openstack trait list
openstack trait show <TRAIT>
openstack trait create <CUSTOM_TRAIT_NAME>

# --- Allocations ---
openstack resource provider usage show <UUID>
openstack allocation candidate list --resource VCPU=2,MEMORY_MB=4096,DISK_GB=50
openstack allocation show <CONSUMER_UUID>
openstack allocation delete <CONSUMER_UUID>
```

## Nova
```bash
# --- Listing & inspecting ---
openstack server list
openstack server list --all-projects          # admin only
openstack server list --host <HYPERVISOR>
openstack server list --status ERROR
openstack server show <SERVER>
openstack server show <SERVER> -f yaml

# --- Creating ---
openstack server create \
  --flavor <FLAVOR> \
  --image <IMAGE> \
  --network <NET_ID_OR_NAME> \
  --key-name <KEYPAIR> \
  <VM_NAME>

openstack server create \
  --flavor <FLAVOR> \
  --image <IMAGE> \
  --boot-from-volume <GB> \
  --network <NET_ID_OR_NAME> \
  --security-group <SG> \
  --user-data <CLOUD_INIT_FILE> \
  --availability-zone <AZ> \
  --hint group=<SERVER_GROUP_ID> \
  <VM_NAME>

# --- Power state ---
openstack server stop <SERVER>
openstack server start <SERVER>
openstack server reboot <SERVER>
openstack server reboot --hard <SERVER>

# --- Pause / suspend / shelve (persist CPU/memory state) ---
openstack server pause <SERVER>
openstack server unpause <SERVER>
openstack server suspend <SERVER>
openstack server resume <SERVER>
openstack server shelve <SERVER>
openstack server unshelve <SERVER>

# --- Lock / unlock (prevent accidental actions) ---
openstack server lock <SERVER>
openstack server unlock <SERVER>

# --- Rebuild & rescue ---
openstack server rebuild --image <IMAGE> <SERVER>
openstack server rescue <SERVER> [--image <IMAGE>]
openstack server unrescue <SERVER>

# --- Resize ---
openstack server resize --flavor <NEW_FLAVOR> <SERVER>
openstack server resize confirm <SERVER>
openstack server resize revert <SERVER>

# --- Image creation from running instance ---
openstack server image create --name <SNAPSHOT_NAME> <SERVER>

# --- Console & logging ---
openstack console url show <SERVER>           # SPICE/noVNC URL
openstack server console log show <SERVER>
openstack server console log show --lines 100 <SERVER>

# --- SSH (via floating IP or direct network) ---
openstack server ssh <SERVER> --login <USER>

# --- Volumes ---
openstack server add volume <SERVER> <VOLUME> [--device /dev/vdb]
openstack server remove volume <SERVER> <VOLUME>
openstack server volume list <SERVER>

# --- Networks & IPs ---
openstack server add network <SERVER> <NETWORK>
openstack server remove network <SERVER> <NETWORK>
openstack server add floating ip <SERVER> <FLOATING_IP>
openstack server remove floating ip <SERVER> <FLOATING_IP>
openstack server port list <SERVER>

# --- Security groups ---
openstack server add security group <SERVER> <SG>
openstack server remove security group <SERVER> <SG>

# --- Metadata ---
openstack server set --property key=value <SERVER>
openstack server unset --property key <SERVER>

# --- Delete ---
openstack server delete <SERVER>
openstack server delete --wait <SERVER>       # block until gone


openstack server migrate <SERVER>             # cold migrate (let scheduler choose)
openstack server migrate --host <TARGET_HOST> <SERVER>
openstack server live migration <SERVER> <TARGET_HOST>
openstack server live migration <SERVER> --block-migration   # shared storage not required

openstack server evacuation <SERVER>          # host dead; rebuild on another
openstack server evacuation <SERVER> --host <TARGET>

openstack server migration list --server <SERVER>
openstack server migration show <SERVER> <MIGRATION_ID>
```

### Server groups (affinity / anti-affinity)
```bash
openstack server group list
openstack server group show <GROUP>
openstack server group create --policy anti-affinity <NAME>
openstack server group create --policy affinity <NAME>
openstack server group create --policy soft-anti-affinity <NAME>
openstack server group delete <GROUP>
```

### Events / Debugging
```bash
openstack server event list <SERVER>
openstack server event show <SERVER> <REQUEST_ID>
openstack compute service list
openstack compute service set --disable --disable-reason "maintenance" <HOST> nova-compute
openstack compute service set --enable <HOST> nova-compute
openstack compute service delete <ID>
```

### Hypervisors & availability
```bash
openstack hypervisor list
openstack hypervisor show <HYPERVISOR>
openstack hypervisor stats show
openstack availability zone list
openstack availability zone list --long
```

### Host aggregates
```bash
openstack aggregate list
openstack aggregate show <AGGREGATE>
openstack aggregate create <NAME>
openstack aggregate create <NAME> --zone <AZ>
openstack aggregate add host <AGGREGATE> <HOSTNAME>
openstack aggregate remove host <AGGREGATE> <HOSTNAME>
openstack aggregate set --property key=value <AGGREGATE>
openstack aggregate unset --property key <AGGREGATE>
openstack aggregate delete <AGGREGATE>
```

### Quotas (Nova)
```bash
openstack quota show
openstack quota show <PROJECT>
openstack quota set --instances 20 --cores 40 --ram 81920 <PROJECT>
openstack quota set --server-groups 10 --server-group-members 5 <PROJECT>
openstack quota delete <PROJECT>              # revert to defaults
openstack limits show --absolute
openstack limits show --rate
```

## Networking

### Networks & subnets
```bash
openstack network list
openstack network show <NETWORK>
openstack network create <NAME>
openstack network create <NAME> \
  --provider-network-type geneve \
  --provider-segment <VNI_ID>           # OVN overlay (geneve is default tunnel type)
openstack network create <NAME> \
  --provider-network-type vlan \
  --provider-physical-network physnet1 \
  --provider-segment <VLAN_ID> \
  --share
openstack network create <NAME> \
  --provider-network-type flat \
  --provider-physical-network physnet1  # external / provider flat network
openstack network set <NETWORK> --enable
openstack network set <NETWORK> --disable
openstack network delete <NETWORK>

openstack subnet list
openstack subnet show <SUBNET>
openstack subnet create \
  --network <NET> \
  --subnet-range <CIDR> \
  --gateway <IP> \
  --dns-nameserver 8.8.8.8 \
  --allocation-pool start=<IP>,end=<IP> \
  <NAME>
openstack subnet set <SUBNET> --dns-nameserver 1.1.1.1
openstack subnet delete <SUBNET>
```

### Ports
```bash
openstack port list
openstack port list --network <NETWORK>
openstack port show <PORT>
openstack port create \
  --network <NETWORK> \
  --fixed-ip subnet=<SUBNET>,ip-address=<IP> \
  --security-group <SG> \
  <NAME>
openstack port set <PORT> --security-group <SG>
openstack port set <PORT> --no-security-group
openstack port set <PORT> --enable
openstack port set <PORT> --disable
openstack port set <PORT> --allowed-address ip-address=<IP>
openstack port unset <PORT> --allowed-address ip-address=<IP>
openstack port delete <PORT>
```

### Floating IPs
```bash
openstack floating ip list
openstack floating ip show <FLOATING_IP>
openstack floating ip create <EXTERNAL_NET>
openstack floating ip set --port <PORT> <FLOATING_IP>      # associate
openstack floating ip unset --port <FLOATING_IP>           # disassociate
openstack floating ip delete <FLOATING_IP>
```

### Routers
```bash
openstack router list
openstack router show <ROUTER>
openstack router create <NAME>
openstack router set --external-gateway <EXT_NET> <ROUTER>
openstack router unset --external-gateway <ROUTER>
openstack router add subnet <ROUTER> <SUBNET>
openstack router remove subnet <ROUTER> <SUBNET>
openstack router add port <ROUTER> <PORT>
openstack router remove port <ROUTER> <PORT>
openstack router set --route destination=<CIDR>,gateway=<IP> <ROUTER>
openstack router unset --route destination=<CIDR>,gateway=<IP> <ROUTER>
openstack router delete <ROUTER>
```

### Security groups
```bash
openstack security group list
openstack security group show <SG>
openstack security group create <NAME> --description "..."
openstack security group set <SG> --description "..."
openstack security group delete <SG>

openstack security group rule list <SG>
openstack security group rule show <RULE_ID>
openstack security group rule create \
  --protocol tcp \
  --dst-port 22 \
  --ingress \
  --remote-ip 0.0.0.0/0 \
  <SG>
openstack security group rule create \
  --protocol icmp \
  --ingress \
  <SG>
openstack security group rule create \
  --protocol tcp \
  --dst-port 1:65535 \
  --egress \
  <SG>
openstack security group rule delete <RULE_ID>
```

### Network segments (operators)
```bash
openstack network segment list
openstack network segment show <SEGMENT>
openstack network segment create \
  --network <NET> \
  --physical-network physnet1 \
  --network-type vlan \
  --segmentation-id <VLAN_ID> \
  <NAME>
openstack network segment delete <SEGMENT>
```

### RBAC policies
```bash
openstack network rbac list
openstack network rbac show <POLICY>
openstack network rbac create \
  --type network \
  --action access_as_shared \
  --target-project <PROJECT> \
  <NETWORK>
openstack network rbac delete <POLICY>
```

### QoS
```bash
openstack network qos policy list
openstack network qos policy show <POLICY>
openstack network qos policy create <NAME>
openstack network qos rule list <POLICY>
openstack network qos rule create \
  --type bandwidth-limit \
  --max-kbps 10000 \
  --max-burst-kbits 10000 \
  <POLICY>
openstack network qos rule delete <POLICY> <RULE_ID>
openstack network qos policy delete <POLICY>
```

### Trunk ports
```bash
openstack network trunk list
openstack network trunk show <TRUNK>
openstack network trunk create --parent-port <PORT> <NAME>
openstack network trunk set \
  --subport port=<PORT>,segmentation-type=vlan,segmentation-id=<ID> \
  <TRUNK>
openstack network trunk unset --subport <PORT> <TRUNK>
openstack network trunk delete <TRUNK>
```

### Agents (operators)
```bash
# With ML2/OVN there are no L3 or DHCP agents. openstack network agent list shows ovn-controller entries (one per compute/network node) and OVN Metadata Agent entries. Router and DHCP scheduling is handled automatically by OVN.

openstack network agent list                           # ovn-controller + OVN-metadata-agent
openstack network agent show <AGENT>
openstack network agent set <AGENT> --disable          # mark chassis unavailable
```

### Gateway chassis
```bash
# Controls where NAT / floating-IP SNAT is performed
# OVN schedules gateway ports automatically; to pin a router's gateway port:
openstack port list --device-owner network:router_gateway --router <ROUTER>
# For explicit chassis binding (advanced / operator use via ovn-nbctl):
sudo ovn-nbctl lrp-set-gateway-chassis <LRP_NAME> <CHASSIS_NAME> <PRIORITY>
sudo ovn-nbctl lrp-get-gateway-chassis <LRP_NAME>
```

### OVN debug (operators)
```bash
# --- Northbound (logical topology) ---
sudo ovn-nbctl show                                    # logical switches, routers, ports
sudo ovn-nbctl ls-list                                 # logical switches
sudo ovn-nbctl lr-list                                 # logical routers
sudo ovn-nbctl lsp-list <LOGICAL_SWITCH>               # logical switch ports
sudo ovn-nbctl lrp-list <LOGICAL_ROUTER>               # logical router ports
sudo ovn-nbctl acl-list <LOGICAL_SWITCH>               # ACLs (security-group rules)
sudo ovn-nbctl dhcp-options-list                       # native DHCP options

# --- Southbound (physical topology / flows) ---
sudo ovn-sbctl show                                    # chassis and port bindings
sudo ovn-sbctl chassis-list                            # registered hypervisors
sudo ovn-sbctl lflow-list                              # all logical flows
sudo ovn-sbctl lflow-list <LOGICAL_ROUTER_UUID>        # flows scoped to a router

# --- Packet tracing (replace values with actual UUIDs/MACs/IPs) ---
sudo ovn-trace <LOGICAL_SWITCH_NAME> \
  'inport=="<LSP_NAME>"; eth.src=<SRC_MAC>; ip4.src=<SRC_IP>; ip4.dst=<DST_IP>; ip.ttl=64'

# --- OVS dataplane on compute ---
sudo ovs-vsctl show
sudo ovs-ofctl dump-flows br-int
sudo ovs-appctl fdb/show br-int
```

## Load Balancing
```bash
# --- Load balancers ---
openstack loadbalancer list
openstack loadbalancer show <LB>
openstack loadbalancer create --name <NAME> --vip-subnet-id <SUBNET_ID>
openstack loadbalancer set <LB> --description "..."
openstack loadbalancer delete <LB>
openstack loadbalancer status show <LB>
openstack loadbalancer stats show <LB>
openstack loadbalancer failover <LB>

# --- Listeners ---
openstack loadbalancer listener list
openstack loadbalancer listener show <LISTENER>
openstack loadbalancer listener create \
  --loadbalancer <LB> \
  --protocol HTTPS \
  --protocol-port 443 \
  --default-tls-container-ref <BARBICAN_CONTAINER_REF> \
  <NAME>
openstack loadbalancer listener create \
  --loadbalancer <LB> \
  --protocol HTTP \
  --protocol-port 80 \
  <NAME>
openstack loadbalancer listener set <LISTENER> --connection-limit 10000
openstack loadbalancer listener delete <LISTENER>

# --- Pools ---
openstack loadbalancer pool list
openstack loadbalancer pool show <POOL>
openstack loadbalancer pool create \
  --listener <LISTENER> \
  --lb-algorithm ROUND_ROBIN \
  --protocol HTTP \
  <NAME>
openstack loadbalancer pool create \
  --lb-algorithm LEAST_CONNECTIONS \
  --protocol HTTP \
  --session-persistence type=SOURCE_IP \
  <NAME>
openstack loadbalancer pool set <POOL> --lb-algorithm LEAST_CONNECTIONS
openstack loadbalancer pool delete <POOL>

# --- Members ---
openstack loadbalancer member list <POOL>
openstack loadbalancer member show <POOL> <MEMBER>
openstack loadbalancer member create \
  --subnet-id <SUBNET> \
  --address <IP> \
  --protocol-port 8080 \
  --weight 1 \
  <POOL>
openstack loadbalancer member set <POOL> <MEMBER> --weight 2
openstack loadbalancer member delete <POOL> <MEMBER>

# --- Health monitors ---
openstack loadbalancer healthmonitor list
openstack loadbalancer healthmonitor show <HM>
openstack loadbalancer healthmonitor create \
  --delay 5 \
  --timeout 10 \
  --max-retries 3 \
  --type HTTP \
  --url-path /health \
  --http-method GET \
  --expected-codes 200 \
  <POOL>
openstack loadbalancer healthmonitor set <HM> --delay 10
openstack loadbalancer healthmonitor delete <HM>

# --- L7 policies (HTTP redirect / URL rewrite) ---
openstack loadbalancer l7policy list
openstack loadbalancer l7policy create \
  --listener <LISTENER> \
  --action REDIRECT_TO_URL \
  --redirect-url https://example.com \
  --position 1 \
  <NAME>
openstack loadbalancer l7rule list <L7POLICY>
openstack loadbalancer l7rule create \
  --compare-type STARTS_WITH \
  --type PATH \
  --value /api \
  <L7POLICY>
openstack loadbalancer l7policy delete <L7POLICY>
```

### Walkthrough: first load balancer
```bash
source <OPENRC_FILE>

# Flavor profiles: single amphora and active-standby
openstack loadbalancer flavorprofile create \
  --name amphora-single-profile --provider amphora \
  --flavor-data '{"loadbalancer_topology": "SINGLE"}'
openstack loadbalancer flavorprofile create \
  --name amphora-active-standby --provider amphora \
  --flavor-data '{"loadbalancer_topology": "ACTIVE_STANDBY"}'

# Flavors on top of the profiles
openstack loadbalancer flavor create --name standalone-lb \
  --flavorprofile amphora-single-profile \
  --description "A non-high availability load balancer for testing." --enable
openstack loadbalancer flavor create --name ha-lb \
  --flavorprofile amphora-active-standby \
  --description "A high availability load balancer for testing." --enable

# Load balancer
openstack loadbalancer create --flavor ha-lb --vip-subnet-id <SUBNET> \
  --wait --name <LB_NAME>

# Listener (frontend)
openstack loadbalancer listener create --name <LISTENER_NAME> \
  --protocol TCP --protocol-port 80 --wait <LB_NAME>

# Pool of backends
openstack loadbalancer pool create --name <POOL_NAME> \
  --lb-algorithm ROUND_ROBIN --listener <LISTENER_NAME> \
  --protocol TCP --wait

# Health check for the backends
openstack loadbalancer healthmonitor create --type TCP \
  --delay 15 --max-retries 4 --timeout 10 --wait <POOL_NAME>

# Add a member, <MEMBER_IP> is the address of the backend instance
openstack server show <SERVER> -c addresses -f json \
  | jq '.addresses["<NETWORK>"][0]' -Mr
openstack loadbalancer member create --subnet-id <SUBNET> \
  --address <MEMBER_IP> --protocol-port <PORT> --wait <POOL_NAME>

# Clean up, removes listeners, pools and members as well
openstack loadbalancer delete <LB_NAME> --cascade
```

## Block Storage
```bash
# --- Volume types ---
openstack volume type list
openstack volume type show <TYPE>
openstack volume type create <NAME>
openstack volume type set <TYPE> --property volume_backend_name=<BACKEND>
openstack volume type delete <TYPE>

# --- Volumes ---
openstack volume list
openstack volume show <VOLUME>
openstack volume create --size <GB> --type <VOLUME_TYPE> <NAME>
openstack volume create --size <GB> --source <SOURCE_VOLUME> <NAME>   # clone
openstack volume create --size <GB> --snapshot <SNAPSHOT> <NAME>      # from snapshot
openstack volume create --size <GB> --image <IMAGE> <NAME>            # from image
openstack volume set <VOLUME> --name <NEW_NAME>
openstack volume set <VOLUME> --description "..."
openstack volume set <VOLUME> --bootable
openstack volume set <VOLUME> --non-bootable
openstack volume set <VOLUME> --read-write
openstack volume set <VOLUME> --read-only
openstack volume delete <VOLUME>
openstack volume delete --force <VOLUME>

# --- Attach / detach ---
openstack volume attach <VOLUME> <SERVER>
openstack volume detach <VOLUME> <SERVER>

# --- Extend ---
openstack volume set <VOLUME> --size <NEW_GB>   # extend (most backends)

# --- Retype ---
openstack volume retype --migration-policy on-demand <VOLUME> <NEW_TYPE>

# --- Snapshots ---
openstack volume snapshot list
openstack volume snapshot show <SNAPSHOT>
openstack volume snapshot create --name <NAME> <VOLUME>
openstack volume snapshot create --name <NAME> --force <VOLUME>        # while in-use
openstack volume snapshot set <SNAPSHOT> --name <NEW_NAME>
openstack volume snapshot delete <SNAPSHOT>

# --- Backups ---
openstack volume backup list
openstack volume backup show <BACKUP>
openstack volume backup create --name <NAME> <VOLUME>
openstack volume backup create --name <NAME> --incremental <VOLUME>
openstack volume backup restore <BACKUP> [<VOLUME>]
openstack volume backup delete <BACKUP>

# --- Volume transfer (move between projects) ---
openstack volume transfer request list
openstack volume transfer request create <VOLUME>
openstack volume transfer request accept <TRANSFER_ID> --auth-key <KEY>
openstack volume transfer request delete <TRANSFER_ID>

# --- QoS ---
openstack volume qos list
openstack volume qos show <QOS>
openstack volume qos create --consumer front-end \
  --property total_iops_sec=1000 <NAME>
openstack volume qos associate <QOS> <VOLUME_TYPE>
openstack volume qos disassociate <QOS> <VOLUME_TYPE>
openstack volume qos delete <QOS>
```

## Manila — Shared Filesystems
```bash
# --- Share types ---
openstack share type list
openstack share type show <TYPE>
openstack share type create <NAME> <DRIVER_HANDLES_SHARE_SERVERS>
openstack share type set <TYPE> --extra-spec <KEY>=<VALUE>
openstack share type delete <TYPE>

# --- Share networks ---
openstack share network list
openstack share network show <SN>
openstack share network create \
  --neutron-net-id <NET> \
  --neutron-subnet-id <SUBNET> \
  --name <NAME>
openstack share network delete <SN>

# --- Shares ---
openstack share list
openstack share show <SHARE>
openstack share create NFS 50 --name <NAME> --share-network <SN>
openstack share create CIFS 100 --name <NAME> --share-type <TYPE>
openstack share set <SHARE> --name <NEW_NAME>
openstack share extend <SHARE> <NEW_SIZE_GB>
openstack share shrink <SHARE> <NEW_SIZE_GB>
openstack share delete <SHARE>

# --- Access rules ---
openstack share access list <SHARE>
openstack share access show <SHARE> <ACCESS_ID>
openstack share access create <SHARE> ip <CIDR>
openstack share access create <SHARE> user <USERNAME>
openstack share access delete <SHARE> <ACCESS_ID>

# --- Snapshots ---
openstack share snapshot list
openstack share snapshot show <SNAPSHOT>
openstack share snapshot create --name <NAME> <SHARE>
openstack share snapshot delete <SNAPSHOT>

# --- Export locations ---
openstack share export location list <SHARE>
```

## Ironic — Bare Metal
```bash
# --- Nodes ---
openstack baremetal node list
openstack baremetal node show <NODE>
openstack baremetal node create \
  --driver ipmi \
  --driver-info ipmi_address=<BMC_IP> \
  --driver-info ipmi_username=<USER> \
  --driver-info ipmi_password=<PASS>
openstack baremetal node set <NODE> --name <NAME>
openstack baremetal node set <NODE> --property memory_mb=32768
openstack baremetal node set <NODE> --property cpus=16
openstack baremetal node set <NODE> --property local_gb=500
openstack baremetal node validate <NODE>
openstack baremetal node manage <NODE>         # move to manageable
openstack baremetal node provide <NODE>        # move to available
openstack baremetal node inspect <NODE>        # in-band/OOB inspection
openstack baremetal node clean <NODE>
openstack baremetal node deploy <NODE>         # manual deploy (testing)
openstack baremetal node undeploy <NODE>
openstack baremetal node maintenance set <NODE> --reason "..."
openstack baremetal node maintenance unset <NODE>
openstack baremetal node delete <NODE>

# --- Ports ---
openstack baremetal port list
openstack baremetal port list --node <UUID>
openstack baremetal port show <PORT>
openstack baremetal port create \
  --node <NODE_UUID> \
  --address <MAC> \
  --pxe-enabled true

# --- Port groups (bonding) ---
openstack baremetal port group list
openstack baremetal port group create --node <UUID> --address <MAC>

# --- Chassis ---
openstack baremetal chassis list
openstack baremetal chassis create --description "rack-01"

# --- Drivers ---
openstack baremetal driver list
openstack baremetal driver show <DRIVER>

# --- Introspection (ironic-inspector) ---
openstack baremetal introspection list
openstack baremetal introspection start <NODE>
openstack baremetal introspection status <NODE>
openstack baremetal introspection data save <NODE>
openstack baremetal introspection abort <NODE>

# --- Allocations ---
openstack baremetal allocation list
openstack baremetal allocation create --resource-class <RC> --name <NAME>
openstack baremetal allocation show <ALLOCATION>
openstack baremetal allocation delete <ALLOCATION>
```

## Designate — DNS
```bash
# --- Zones ---
openstack zone list
openstack zone show <ZONE>
openstack zone create --email admin@example.com <ZONE_FQDN>
openstack zone create \
  --type SECONDARY \
  --masters <PRIMARY_NAMESERVER_IP> \
  <ZONE_FQDN>
openstack zone set <ZONE> --description "..."
openstack zone delete <ZONE>
openstack zone transfer request create <ZONE>
openstack zone transfer accept request <TRANSFER_ID> --key <KEY>

# --- Record sets ---
openstack recordset list <ZONE>
openstack recordset list <ZONE> --type A
openstack recordset show <ZONE> <RECORDSET>
openstack recordset create <ZONE> <NAME> --type A --record <IP>
openstack recordset create <ZONE> <NAME> --type AAAA --record <IPV6>
openstack recordset create <ZONE> <NAME> --type CNAME --record <TARGET>
openstack recordset create <ZONE> <NAME> --type MX --record "10 mail.example.com."
openstack recordset create <ZONE> <NAME> --type TXT --record "v=spf1 mx -all"
openstack recordset set <ZONE> <RECORDSET> --record <NEW_IP>
openstack recordset delete <ZONE> <RECORDSET>

# --- Nameservers & pools (operators) ---
openstack dns service status list
openstack ptr record list
openstack ptr record set <FLOATINGIP_ID> <FQDN>
openstack ptr record unset <FLOATINGIP_ID>
```

## Service & Control Plane Ops
```bash
# --- Service status ---
openstack compute service list
openstack network agent list
openstack volume service list
openstack baremetal conductor list
```

## Debugging
```bash
# --- RabbitMQ ---
rabbitmqctl list_queues name messages consumers
rabbitmqctl list_connections
rabbitmqctl list_exchanges
rabbitmqctl node_health_check

# --- MariaDB / Galera ---
mysql -e "SHOW STATUS LIKE 'wsrep%';"
mysql -e "SHOW PROCESSLIST;"

# --- OVN (Neutron ML2/OVN) ---
sudo ovn-nbctl show                          # logical topology
sudo ovn-sbctl show                          # chassis / binding topology
sudo ovn-nbctl ls-list
sudo ovn-nbctl lr-list
sudo ovn-sbctl chassis-list
sudo ovs-vsctl show                          # OVS dataplane on compute
sudo ovs-ofctl dump-flows br-int

# --- Network namespaces (ML2/OVN has no qrouter or qdhcp; only ovnmeta-*) ---
ip netns list                                # expect ovnmeta-<NET_UUID> namespaces only
sudo ip netns exec ovnmeta-<NET_UUID> ip addr show

# --- Nova reset / recovery patterns (follow runbooks; do not run blindly) ---
# Disable a failed compute host:
openstack compute service set --disable --disable-reason "host down" <HOST> nova-compute
# Evacuate all instances off the host (admin):
# openstack server list --all-projects --host <HOST> -f value -c ID \
#   | xargs -I{} openstack server evacuate {}

# --- Cinder volume stuck in detaching ---
# openstack volume set --state available <VOLUME>   # use with care; operator runbook

# --- Neutron: reschedule DHCP agent ---
openstack network dhcp agent remove network <DEAD_AGENT_ID> <NETWORK>
openstack network dhcp agent add network <NEW_AGENT_ID> <NETWORK>
```
