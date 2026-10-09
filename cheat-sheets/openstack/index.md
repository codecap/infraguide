---
layout: default
title: "OpenStack CLI Cheat Sheet: Common Commands | infraguide.org"
description: "OpenStack CLI cheat sheet with commands for identity, instances, images, volumes, networking, OVN, load balancing, Manila, Ironic and Designate. Copy and paste ready."
breadcrumbs:
  - name: cheat-sheets
    url: /cheat-sheets/
  - name: openstack
---

# OpenStack CLI Cheat Sheet

Common openstack commands, grouped by service. Replace the values in angle brackets.

{% include cheat-sheet-notation.md %}

Want to see these commands in context? Build the [OpenStack lab](/learn/openstack/).

{% include cheat-sheet-toc.md %}

## CLI basics

### Help and debugging
```bash
openstack command list
# show the REST calls and responses
openstack --debug <COMMAND>
```

### Output formatting
```bash
# Format: table (default), json, yaml, csv or value
openstack server list -f json
openstack server list -f csv --quote minimal
openstack server show <SERVER> -f shell

# Columns (repeat -c per column)
openstack server list -c ID -c Name -c Status -c Networks
openstack volume list -c ID -c Name -c Size -c Status

# Filter and sort
openstack server list --status ACTIVE
openstack server list --name <REGEX>
openstack server list --sort-column Name
openstack server list --sort-column Status --sort-descending

# Paging
# the last ID of a page is the marker for the next one
openstack server list --limit <N> --marker <LAST_ID>

# Scripting
# a single value, one per line
openstack server show <SERVER> -f value -c id
openstack server list -f value -c ID | xargs -I{} openstack server show {}
```

## Identity

### Domains
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

openstack domain set <DOMAIN_ID> --description "<DESCRIPTION>"
```

### Users
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

# User in a domain with a default project
openstack user create --domain <DOMAIN> --project <PROJECT> \
  --description "<DESCRIPTION>" --email <EMAIL> --password <PASSWORD> \
  --enable <USER>

# Enable, disable, change the password
openstack user set <USER_ID> --enable
openstack user set <USER_ID> --disable
openstack user set <USER_ID> --password <PASSWORD>
```

### Groups
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

# Group in the default domain: add and check a member
openstack group create --domain Default --description "<DESCRIPTION>" <GROUP>
openstack group add user <GROUP> <USER>
openstack group contains user <GROUP> <USER>

# Group in another domain (members come from that domain too)
openstack group create --domain <DOMAIN> --description "<DESCRIPTION>" <GROUP>
openstack group add user      --group-domain <DOMAIN> <GROUP> <USER>
openstack group contains user --group-domain <DOMAIN> <GROUP> <USER>
```

### Projects
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

### Roles
```bash
# assign role on project
openstack role add --project <PROJECT_ID> \
  {--user <USER_ID>|--group <GROUP_ID>} <ROLE_NAME>
# remove role on project
openstack role remove --project <PROJECT_ID> \
  {--user <USER_ID>|--group <GROUP_ID>} <ROLE_NAME>

# Role in a domain
openstack role create --domain Default <ROLE>

# Role assignments (names instead of IDs)
openstack role list
openstack role show <ROLE_ID>
openstack role assignment list --names
openstack role assignment list --user <USER_ID> --names

# Implied roles (the prior role includes the implied one)
openstack implied role list
openstack implied role create <PRIOR_ROLE> --implied-role <IMPLIED_ROLE>
openstack implied role delete <PRIOR_ROLE> --implied-role <IMPLIED_ROLE>
```

### Quotas
```bash
# Default and project quotas
# list default quotas
openstack quota show --default
# update default quotas
openstack quota set <KEY> <VALUE> --class default
# list project quotas
openstack quota show <PROJECT_ID>
# update project quotas
openstack quota set <KEY> <VALUE> <PROJECT_ID>

# Compute quotas and limits
openstack quota show
openstack quota show <PROJECT>
openstack quota set --instances <N> --cores <N> --ram <MB> <PROJECT>
openstack quota set --server-groups <N> --server-group-members <N> <PROJECT>
openstack quota delete <PROJECT>              # revert to defaults
openstack limits show --absolute
openstack limits show --rate

# Block storage and network quotas
openstack quota set --volumes <N> --gigabytes <GB> --snapshots <N> <PROJECT>
openstack quota set --networks <N> --subnets <N> --routers <N> \
  --floating-ips <N> --secgroups <N> <PROJECT>
openstack limits show --absolute --project <PROJECT>
```

### Catalog & services
```bash
# list OpenStack services
openstack catalog list

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

# Endpoints and services
openstack endpoint list
openstack endpoint list --service <SERVICE>
openstack endpoint show <ENDPOINT_ID>
openstack service show <SERVICE>

# Regions
openstack region list
openstack region show <REGION>
openstack region create <REGION>
```

### Authentication
```bash
# Credentials: openrc file or clouds.yaml
source <OPENRC_FILE>
env | grep OS_
openstack --os-cloud <CLOUD> server list

# Tokens
openstack token issue
openstack token issue -f yaml
openstack token revoke <TOKEN>

# Application credentials: scoped and expiring, for scripts
openstack application credential create <NAME> --role <ROLE> --expiration <ISO8601_DATE>
openstack application credential list
openstack application credential show <NAME_OR_ID>
openstack application credential delete <NAME_OR_ID>
```

## Compute

### Flavors
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

# Baremetal flavor
# schedule on the custom resource class of the node, not on VCPU / RAM / disk
openstack flavor create --ram <RAM_MB> --disk <DISK_GB> --vcpus <VCPUS> <FLAVOR_NAME>
openstack flavor set <FLAVOR_NAME> \
  --property resources:CUSTOM_<RESOURCE_CLASS>=1 \
  --property resources:VCPU=0 \
  --property resources:MEMORY_MB=0 \
  --property resources:DISK_GB=0

# All flavors, ephemeral disk, aggregate extra spec
openstack flavor list --all
openstack flavor create --vcpus <VCPUS> --ram <RAM_MB> --disk <DISK_GB> \
  --ephemeral <EPHEMERAL_GB> <FLAVOR_NAME>
# extra spec: schedule onto host aggregates with a matching property
openstack flavor set <FLAVOR> --property aggregate_instance_extra_specs:<KEY>=<VALUE>
openstack flavor unset <FLAVOR> --property <KEY>
```

### Key pairs
```bash
# list key pairs
openstack keypair list
# show key pair details
openstack keypair show <KEY_PAIR_NAME>
# create key pair
openstack keypair create --private-key <FILE_PATH> <KEY_PAIR_NAME>
# delete key pair
openstack keypair delete <KEY_PAIR_NAME>

# Upload an existing public key or generate a pair
openstack keypair create --public-key <PUBLIC_KEY_FILE> <KEY_PAIR_NAME>
openstack keypair create <KEY_PAIR_NAME> > <PRIVATE_KEY_FILE>
```

### Instances
```bash
# Listing & inspecting
openstack server list
openstack server list --all-projects          # admin only
openstack server list --host <HYPERVISOR>
openstack server list --status ERROR
openstack server show <SERVER>
openstack server show <SERVER> -f yaml

# Creating
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

# Power state
openstack server stop <SERVER>
openstack server start <SERVER>
openstack server reboot <SERVER>
openstack server reboot --hard <SERVER>

# Pause / suspend / shelve (persist CPU/memory state)
openstack server pause <SERVER>
openstack server unpause <SERVER>
openstack server suspend <SERVER>
openstack server resume <SERVER>
openstack server shelve <SERVER>
openstack server unshelve <SERVER>

# Lock / unlock (prevent accidental actions)
openstack server lock <SERVER>
openstack server unlock <SERVER>

# Rebuild & rescue
openstack server rebuild --image <IMAGE> <SERVER>
openstack server rescue <SERVER> [--image <IMAGE>]
openstack server unrescue <SERVER>

# Resize
openstack server resize --flavor <NEW_FLAVOR> <SERVER>
openstack server resize confirm <SERVER>
openstack server resize revert <SERVER>

# Image creation from running instance
openstack server image create --name <SNAPSHOT_NAME> <SERVER>

# Console & logging
openstack console url show <SERVER>           # SPICE/noVNC URL
openstack server console log show <SERVER>
openstack server console log show --lines 100 <SERVER>

# SSH (via floating IP or direct network)
openstack server ssh <SERVER> --login <USER>

# Volumes
openstack server add volume <SERVER> <VOLUME> [--device <DEVICE_PATH>]
openstack server remove volume <SERVER> <VOLUME>
openstack server volume list <SERVER>

# Networks & IPs
openstack server add network <SERVER> <NETWORK>
openstack server remove network <SERVER> <NETWORK>
openstack server add floating ip <SERVER> <FLOATING_IP>
openstack server remove floating ip <SERVER> <FLOATING_IP>
openstack server port list <SERVER>

# Security groups
openstack server add security group <SERVER> <SG>
openstack server remove security group <SERVER> <SG>

# Metadata
openstack server set --property key=value <SERVER>
openstack server unset --property key <SERVER>

# Delete
openstack server delete <SERVER>
openstack server delete --wait <SERVER>       # block until gone

# Migrate & evacuate
openstack server migrate <SERVER>             # cold migrate (let scheduler choose)
openstack server migrate --host <TARGET_HOST> <SERVER>
openstack server migrate --live-migration --host <TARGET_HOST> <SERVER>
openstack server migrate --live-migration --block-migration <SERVER>   # shared storage not required

openstack server evacuate <SERVER>           # host dead; rebuild on another
openstack server evacuate --host <TARGET_HOST> <SERVER>

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

### Events & compute services
```bash
# Server events
openstack server event list <SERVER>
openstack server event show <SERVER> <REQUEST_ID>

# Compute services
openstack compute service list
# take a host out of scheduling, and back
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

# Private image with properties
openstack image create --file <IMAGE_FILE> --private \
  --property description="<DESCRIPTION>" <IMAGE_NAME>
openstack image set --min-ram <MB> <IMAGE_NAME>
openstack image set --property os_shutdown_timeout=<SECONDS> <IMAGE_NAME>
openstack image show <IMAGE_NAME>

# Import from a URL
openstack image import --disk-format qcow2 --container-format bare \
  --uri <URI> <IMAGE_NAME>

# Hardware properties (the guest sees virtio devices)
openstack image set <IMAGE> --property hw_disk_bus=virtio
openstack image set <IMAGE> --property hw_scsi_model=virtio-scsi
openstack image set <IMAGE> --property hw_vif_model=virtio
openstack image set <IMAGE> --min-disk <GB> --min-ram <MB>
openstack image unset <IMAGE> --property <KEY>

# Visibility
openstack image list --public
openstack image list --private
openstack image list --status active
openstack image set <IMAGE> --public
openstack image set <IMAGE> --private
openstack image set <IMAGE> --shared

# Share an image with another project
openstack image member create <IMAGE> <PROJECT>
openstack image member list <IMAGE>
openstack image member delete <IMAGE> <PROJECT>
# the other project accepts it
openstack image set --accept <IMAGE>

# Deactivate / activate (unusable but not deleted)
openstack image set <IMAGE> --deactivate
openstack image set <IMAGE> --activate
```

### Octavia amphora image
```bash
# Octavia amphora image
# the release is taken from the kolla-ansible config
os_release=$(
  cat $VIRTUAL_ENV/share/kolla-ansible/ansible/group_vars/all.yml \
    | y2j \
    | jq .openstack_release -Mr
)
amphora_image_file=octavia-amphora-haproxy-${os_release}.qcow2
amphora_image_url="https://<IMAGE_HOST>/$amphora_image_file"
mkdir -p ~/cloud-images/
curl -o ~/cloud-images/$amphora_image_file $amphora_image_url

openstack image create <IMAGE_NAME> \
  --container-format bare --disk-format qcow2 --private --tag amphora \
  --file ~/cloud-images/$amphora_image_file \
  --property hw_architecture='x86_64' --property hw_rng_model=virtio
```

## Block storage
```bash
# Volume types
openstack volume type list
openstack volume type show <TYPE>
openstack volume type create <NAME>
openstack volume type set <TYPE> --property volume_backend_name=<BACKEND>
openstack volume type delete <TYPE>

# Volumes
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

# Attach / detach
openstack volume attach <VOLUME> <SERVER>
openstack volume detach <VOLUME> <SERVER>

# Extend
openstack volume set <VOLUME> --size <NEW_GB>   # extend (most backends)

# Retype
openstack volume retype --migration-policy on-demand <VOLUME> <NEW_TYPE>

# Snapshots
openstack volume snapshot list
openstack volume snapshot show <SNAPSHOT>
openstack volume snapshot create --name <NAME> <VOLUME>
openstack volume snapshot create --name <NAME> --force <VOLUME>        # while in-use
openstack volume snapshot set <SNAPSHOT> --name <NEW_NAME>
openstack volume snapshot delete <SNAPSHOT>

# Backups
openstack volume backup list
openstack volume backup show <BACKUP>
openstack volume backup create --name <NAME> <VOLUME>
openstack volume backup create --name <NAME> --incremental <VOLUME>
openstack volume backup restore <BACKUP> [<VOLUME>]
openstack volume backup delete <BACKUP>

# Volume transfer (move between projects)
openstack volume transfer request list
openstack volume transfer request create <VOLUME>
openstack volume transfer request accept <TRANSFER_ID> --auth-key <KEY>
openstack volume transfer request delete <TRANSFER_ID>

# QoS
openstack volume qos list
openstack volume qos show <QOS>
openstack volume qos create --consumer front-end \
  --property total_iops_sec=1000 <NAME>
openstack volume qos associate <QOS> <VOLUME_TYPE>
openstack volume qos disassociate <QOS> <VOLUME_TYPE>
openstack volume qos delete <QOS>
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
  --provider-physical-network <PHYSNET> \
  --provider-segment <VLAN_ID> \
  --share
openstack network create <NAME> \
  --provider-network-type flat \
  --provider-physical-network <PHYSNET>  # external / provider flat network
openstack network set <NETWORK> --enable
openstack network set <NETWORK> --disable
openstack network delete <NETWORK>

openstack subnet list
openstack subnet show <SUBNET>
openstack subnet create \
  --network <NET> \
  --subnet-range <CIDR> \
  --gateway <IP> \
  --dns-nameserver <DNS_IP> \
  --allocation-pool start=<IP>,end=<IP> \
  <NAME>
openstack subnet set <SUBNET> --dns-nameserver <DNS_IP>
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
  --physical-network <PHYSNET> \
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

### Provider network setup (example)
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

## OVN

### Agents (operators)
```bash
# ML2/OVN agents
# there are no L3 or DHCP agents, the list shows ovn-controller (one per compute/network node)
# and OVN metadata agent entries; OVN schedules routers and DHCP automatically

openstack network agent list                           # ovn-controller + OVN-metadata-agent
openstack network agent show <AGENT>
openstack network agent set <AGENT> --disable          # mark chassis unavailable
```

### Gateway chassis
```bash
# Gateway chassis: where NAT / floating-IP SNAT is performed
# OVN schedules gateway ports automatically; to pin a router's gateway port:
openstack port list --device-owner network:router_gateway --router <ROUTER>
# For explicit chassis binding (advanced / operator use via ovn-nbctl):
sudo ovn-nbctl lrp-set-gateway-chassis <LRP_NAME> <CHASSIS_NAME> <PRIORITY>
sudo ovn-nbctl lrp-get-gateway-chassis <LRP_NAME>
```

### OVN debug (operators)
```bash
# Northbound (logical topology)
sudo ovn-nbctl show                                    # logical switches, routers, ports
sudo ovn-nbctl ls-list                                 # logical switches
sudo ovn-nbctl lr-list                                 # logical routers
sudo ovn-nbctl lsp-list <LOGICAL_SWITCH>               # logical switch ports
sudo ovn-nbctl lrp-list <LOGICAL_ROUTER>               # logical router ports
sudo ovn-nbctl acl-list <LOGICAL_SWITCH>               # ACLs (security-group rules)
sudo ovn-nbctl dhcp-options-list                       # native DHCP options

# Southbound (physical topology / flows)
sudo ovn-sbctl show                                    # chassis and port bindings
sudo ovn-sbctl chassis-list                            # registered hypervisors
sudo ovn-sbctl lflow-list                              # all logical flows
sudo ovn-sbctl lflow-list <LOGICAL_ROUTER_UUID>        # flows scoped to a router

# Packet tracing (replace values with actual UUIDs/MACs/IPs)
sudo ovn-trace <LOGICAL_SWITCH_NAME> \
  'inport=="<LSP_NAME>"; eth.src=<SRC_MAC>; ip4.src=<SRC_IP>; ip4.dst=<DST_IP>; ip.ttl=64'

# OVS dataplane on compute
sudo ovs-vsctl show
sudo ovs-ofctl dump-flows br-int
sudo ovs-appctl fdb/show br-int
```

### OVS bridges & ports
```bash
# Bridges and ports
# br-int is the integration bridge, br-ex or similar the provider one
sudo ovs-vsctl show
sudo ovs-vsctl list-br
sudo ovs-vsctl list-ports <BRIDGE>
sudo ovs-vsctl list interface <INTERFACE>
sudo ovs-vsctl get Interface <INTERFACE> statistics
# which OpenFlow port number belongs to an interface
sudo ovs-ofctl show <BRIDGE>
sudo ovs-vsctl find interface ofport=<PORT_NUMBER>

# Provider network to bridge mapping (one entry per physnet)
sudo ovs-vsctl get open . external-ids:ovn-bridge-mappings
sudo ovs-vsctl set open . external-ids:ovn-bridge-mappings=<PHYSNET>:<BRIDGE>
# all OVN settings of this node: chassis name, encap type and IP, SB database
sudo ovs-vsctl get open . external-ids

# Bonds and VLAN settings of a port
sudo ovs-appctl bond/show <BOND>
sudo ovs-appctl lacp/show <BOND>
sudo ovs-vsctl get port <PORT> tag trunks vlan_mode

# ⚠️ Neutron and ovn-controller own the integration bridge and its ports,
# do not add or delete ports or flows there by hand
```

### OVS flows & tracing
```bash
# Flows of a bridge
# the whole bridge or one table
sudo ovs-ofctl dump-flows <BRIDGE>
sudo ovs-ofctl dump-flows <BRIDGE> table=<TABLE>
# flows matching a packet, with port and interface names instead of numbers
sudo ovs-ofctl --names dump-flows <BRIDGE> "<MATCH>"
# port counters and descriptions
sudo ovs-ofctl dump-ports <BRIDGE>
sudo ovs-ofctl dump-ports-desc <BRIDGE>
# watch flow changes live
sudo ovs-ofctl monitor <BRIDGE> watch:

# Datapath flows in use (kernel or userspace datapath)
sudo ovs-appctl dpctl/dump-flows
sudo ovs-dpctl show

# Trace a packet through the OpenFlow tables
sudo ovs-appctl ofproto/trace <BRIDGE> \
  in_port=<PORT>,<PROTOCOL>,dl_src=<SRC_MAC>,dl_dst=<DST_MAC>,nw_src=<SRC_IP>,nw_dst=<DST_IP>

# Connection tracking (security groups and NAT)
sudo ovs-appctl dpctl/dump-conntrack
# ⚠️ drops all tracked connections on this node
sudo ovs-appctl dpctl/flush-conntrack

# Capture on an OVS port
sudo ovs-tcpdump -i <INTERFACE> -n
```

### OVN database queries
```bash
# Northbound DB
# Neutron creates this data, read it but do not edit it
sudo ovn-nbctl list Logical_Switch_Port <LSP_NAME>
sudo ovn-nbctl list Logical_Router_Port
sudo ovn-nbctl lr-nat-list <LOGICAL_ROUTER>
sudo ovn-nbctl lr-route-list <LOGICAL_ROUTER>
sudo ovn-nbctl lb-list

# Chassis and port bindings
sudo ovn-sbctl list chassis
sudo ovn-sbctl get chassis <CHASSIS_NAME> hostname
sudo ovn-sbctl find chassis name=<CHASSIS_NAME>
sudo ovn-sbctl list port_binding
sudo ovn-sbctl find port_binding logical_port=<LSP_NAME>

# Remote database (6641 northbound, 6642 southbound)
sudo ovn-nbctl --db=tcp:<DB_IP>:6641 show
sudo ovn-sbctl --db=tcp:<DB_IP>:6642 lflow-list

# OVS database
sudo ovsdb-client list-dbs
sudo ovsdb-client list-tables Open_vSwitch
sudo ovsdb-client dump unix:/var/run/openvswitch/db.sock Open_vSwitch
sudo ovsdb-client monitor Open_vSwitch Port

# Services on a node
# the unit is openvswitch or openvswitch-switch depending on the distro;
# with kolla-ansible they run as containers: docker logs ovn_controller
sudo systemctl status openvswitch-switch ovn-controller
sudo journalctl -u ovn-controller -f
```

## Load balancing (Octavia)
```bash
# Load balancers
openstack loadbalancer list
openstack loadbalancer show <LB>
openstack loadbalancer create --name <NAME> --vip-subnet-id <SUBNET_ID>
openstack loadbalancer set <LB> --description "..."
openstack loadbalancer delete <LB>
openstack loadbalancer status show <LB>
openstack loadbalancer stats show <LB>
openstack loadbalancer failover <LB>

# Listeners
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

# Pools
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

# Members
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

# Health monitors
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

# L7 policies (HTTP redirect / URL rewrite)
openstack loadbalancer l7policy list
openstack loadbalancer l7policy create \
  --listener <LISTENER> \
  --action REDIRECT_TO_URL \
  --redirect-url <URL> \
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
# Credentials
source <OPENRC_FILE>

# Flavor profiles: single amphora and active-standby
openstack loadbalancer flavorprofile create \
  --name <SINGLE_PROFILE> --provider amphora \
  --flavor-data '{"loadbalancer_topology": "SINGLE"}'
openstack loadbalancer flavorprofile create \
  --name <HA_PROFILE> --provider amphora \
  --flavor-data '{"loadbalancer_topology": "ACTIVE_STANDBY"}'

# Flavors on top of the profiles
openstack loadbalancer flavor create --name <SINGLE_FLAVOR> \
  --flavorprofile <SINGLE_PROFILE> \
  --description "A non-high availability load balancer for testing." --enable
openstack loadbalancer flavor create --name <HA_FLAVOR> \
  --flavorprofile <HA_PROFILE> \
  --description "A high availability load balancer for testing." --enable

# Load balancer
openstack loadbalancer create --flavor <HA_FLAVOR> --vip-subnet-id <SUBNET> \
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

# Member
# <MEMBER_IP> is the address of the backend instance
openstack server show <SERVER> -c addresses -f json \
  | jq '.addresses["<NETWORK>"][0]' -Mr
openstack loadbalancer member create --subnet-id <SUBNET> \
  --address <MEMBER_IP> --protocol-port <PORT> --wait <POOL_NAME>

# Clean up
# removes listeners, pools and members as well
openstack loadbalancer delete <LB_NAME> --cascade
```

## Placement
```bash
# Resource providers
openstack resource provider list
openstack resource provider show <UUID>
openstack resource provider create <NAME>
openstack resource provider delete <UUID>

# Inventory
openstack resource provider inventory list <UUID>
openstack resource provider inventory show <UUID> <RESOURCE_CLASS>
openstack resource provider inventory set <UUID> <RESOURCE_CLASS> \
  --total <N> --reserved <N> --min-unit <N> --max-unit <N> --step-size <N>
openstack resource provider inventory delete <UUID> <RESOURCE_CLASS>

# Aggregates
openstack resource provider aggregate list <UUID>
openstack resource provider aggregate set <UUID> --aggregate <AGG_UUID>

# Traits
openstack resource provider trait list <UUID>
openstack resource provider trait set <UUID> --trait <TRAIT_NAME>
openstack resource provider trait delete <UUID>
openstack trait list
openstack trait show <TRAIT>
openstack trait create <CUSTOM_TRAIT_NAME>

# Allocations
openstack resource provider usage show <UUID>
openstack allocation candidate list --resource VCPU=2,MEMORY_MB=4096,DISK_GB=50
openstack allocation show <CONSUMER_UUID>
openstack allocation delete <CONSUMER_UUID>
```

## Shared filesystems (Manila)
```bash
# Share types
openstack share type list
openstack share type show <TYPE>
openstack share type create <NAME> <DRIVER_HANDLES_SHARE_SERVERS>
openstack share type set <TYPE> --extra-spec <KEY>=<VALUE>
openstack share type delete <TYPE>

# Share networks
openstack share network list
openstack share network show <SN>
openstack share network create \
  --neutron-net-id <NET> \
  --neutron-subnet-id <SUBNET> \
  --name <NAME>
openstack share network delete <SN>

# Shares
openstack share list
openstack share show <SHARE>
openstack share create NFS 50 --name <NAME> --share-network <SN>
openstack share create CIFS 100 --name <NAME> --share-type <TYPE>
openstack share set <SHARE> --name <NEW_NAME>
openstack share extend <SHARE> <NEW_SIZE_GB>
openstack share shrink <SHARE> <NEW_SIZE_GB>
openstack share delete <SHARE>

# Access rules
openstack share access list <SHARE>
openstack share access show <SHARE> <ACCESS_ID>
openstack share access create <SHARE> ip <CIDR>
openstack share access create <SHARE> user <USERNAME>
openstack share access delete <SHARE> <ACCESS_ID>

# Snapshots
openstack share snapshot list
openstack share snapshot show <SNAPSHOT>
openstack share snapshot create --name <NAME> <SHARE>
openstack share snapshot delete <SNAPSHOT>

# Export locations
openstack share export location list <SHARE>
```

## Bare metal (Ironic)
```bash
# Nodes
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

# Ports
openstack baremetal port list
openstack baremetal port list --node <UUID>
openstack baremetal port show <PORT>
openstack baremetal port create \
  --node <NODE_UUID> \
  --address <MAC> \
  --pxe-enabled true

# Port groups (bonding)
openstack baremetal port group list
openstack baremetal port group create --node <UUID> --address <MAC>

# Chassis
openstack baremetal chassis list
openstack baremetal chassis create --description "rack-01"

# Drivers
openstack baremetal driver list
openstack baremetal driver show <DRIVER>

# Introspection (ironic-inspector)
openstack baremetal introspection list
openstack baremetal introspection start <NODE>
openstack baremetal introspection status <NODE>
openstack baremetal introspection data save <NODE>
openstack baremetal introspection abort <NODE>

# Allocations
openstack baremetal allocation list
openstack baremetal allocation create --resource-class <RC> --name <NAME>
openstack baremetal allocation show <ALLOCATION>
openstack baremetal allocation delete <ALLOCATION>
```

## DNS (Designate)
```bash
# Zones
openstack zone list
openstack zone show <ZONE>
openstack zone create --email <EMAIL> <ZONE_FQDN>
openstack zone create \
  --type SECONDARY \
  --masters <PRIMARY_NAMESERVER_IP> \
  <ZONE_FQDN>
openstack zone set <ZONE> --description "..."
openstack zone delete <ZONE>
openstack zone transfer request create <ZONE>
openstack zone transfer accept request <TRANSFER_ID> --key <KEY>

# Record sets
openstack recordset list <ZONE>
openstack recordset list <ZONE> --type A
openstack recordset show <ZONE> <RECORDSET>
openstack recordset create <ZONE> <NAME> --type A --record <IP>
openstack recordset create <ZONE> <NAME> --type AAAA --record <IPV6>
openstack recordset create <ZONE> <NAME> --type CNAME --record <TARGET>
openstack recordset create <ZONE> <NAME> --type MX --record "<PRIORITY> <MAIL_SERVER_FQDN>"
openstack recordset create <ZONE> <NAME> --type TXT --record "v=spf1 mx -all"
openstack recordset set <ZONE> <RECORDSET> --record <NEW_IP>
openstack recordset delete <ZONE> <RECORDSET>

# Nameservers & pools (operators)
openstack dns service status list
openstack ptr record list
openstack ptr record set <FLOATINGIP_ID> <FQDN>
openstack ptr record unset <FLOATINGIP_ID>
```

## Operations

### Service status
```bash
# Service status
openstack compute service list
openstack network agent list
openstack volume service list
openstack baremetal conductor list
```

### Compute node
```bash
# Instances as libvirt sees them
# <INSTANCE_UUID> is the Nova server ID
sudo virsh list --all
sudo virsh dominfo <INSTANCE_UUID>

# Logs
# with kolla-ansible: docker logs nova_compute
sudo journalctl -u openstack-nova-compute -f

# Can the scheduler place it?
openstack allocation candidate list --resource VCPU=<N>,MEMORY_MB=<MB>,DISK_GB=<GB>

# Console log of the instance
openstack server console log show --lines 200 <SERVER>
```

### Debugging
```bash
# RabbitMQ
rabbitmqctl list_queues name messages consumers
rabbitmqctl list_connections
rabbitmqctl list_exchanges
rabbitmqctl node_health_check

# MariaDB / Galera
mysql -e "SHOW STATUS LIKE 'wsrep%';"
mysql -e "SHOW PROCESSLIST;"

# OVN (Neutron ML2/OVN)
sudo ovn-nbctl show                          # logical topology
sudo ovn-sbctl show                          # chassis / binding topology
sudo ovn-nbctl ls-list
sudo ovn-nbctl lr-list
sudo ovn-sbctl chassis-list
sudo ovs-vsctl show                          # OVS dataplane on compute
sudo ovs-ofctl dump-flows br-int

# Network namespaces (ML2/OVN has no qrouter or qdhcp; only ovnmeta-*)
ip netns list                                # expect ovnmeta-<NET_UUID> namespaces only
sudo ip netns exec ovnmeta-<NET_UUID> ip addr show

# Nova reset / recovery patterns (follow runbooks; do not run blindly)
# Disable a failed compute host:
openstack compute service set --disable --disable-reason "host down" <HOST> nova-compute
# Evacuate all instances off the host (admin):
# openstack server list --all-projects --host <HOST> -f value -c ID \
#   | xargs -I{} openstack server evacuate {}

# Cinder volume stuck in detaching
# openstack volume set --state available <VOLUME>   # use with care; operator runbook

# Neutron: reschedule DHCP agent
openstack network dhcp agent remove network <DEAD_AGENT_ID> <NETWORK>
openstack network dhcp agent add network <NEW_AGENT_ID> <NETWORK>
```
