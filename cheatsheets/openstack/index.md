---
layout: default
title: OpenStack
breadcrumbs:
  - name: cheatsheets
    url: /cheatsheets/
  - name: openstack
---


## List OpenStack services
openstack catalog list

## Domains
List domains
openstack domain list
Create domain
openstack domain create <domain name>
Update domain
openstack domain set <key> <value> <domain ID>
Delete domain
openstack domain delete <domain ID>

## Users
List users
openstack user list
Show user details
openstack user show <user ID>
Create user
openstack user create --password <password> <user name>
Update user
openstack user set <key> <value> <user ID>
Set user password
openstack user password set
Delete user
openstack user delete <user ID>

## Groups
List groups
openstack group list
Show group details
openstack group show <group ID>
Create group
openstack group create <group name>
Show domain details
openstack domain show <domain ID>
1Groups – continued
Update group
openstack group set <key> <value> <group ID>
Add user to group
openstack group add user <group ID> <user ID>
Remove user from
openstack group remove user <group ID> <user ID>
Delete group
openstack group delete <group ID>

## Projects
List projects
openstack project list
Create image
openstack image create --disk-format qcow2 \​
--file <file path> <image name>
Update image
openstack image set <key> <value> <image ID>
Delete image
openstack image delete <image ID>

## Flavors
List flavors
openstack flavor list
Show flavor details
openstack flavor show <flavor name>
Create project
openstack project create <project name>Create flavor
openstack flavor create --vcpus <vCPUs> --ram <RAM [MB]> \​
--disk <Disk [GB]> <flavor name>
Update project
openstack project set <key> <value> <project ID>Update flavor
openstack flavor set <key> <value> <flavor name>
Delete project
openstack project delete <project ID>Delete flavor
openstack flavor delete <flavor name>
RolesNetworks
Assign role on project
openstack role add --project <project ID> \​
[--user <user ID> | --group <group ID>] <role name>List networks
openstack network list
Remove role on project
openstack role remove --project <project ID> \​
[--user <user ID> | --group <group ID>] <role name>


## Images
List images
openstack image list
Show image details
openstack image show <image ID>
Show network details
openstack network show <network ID>
Create network
openstack network create <network name>
Update network
openstack network set <key> <value> <network ID>
Delete network
openstack network delete <network ID>
2SubnetsSecurity groups

## List subnets
openstack subnet listList security groups
openstack security group list
Show subnet details
openstack subnet show <subnet ID>Show security group details
openstack security group show <security group ID>
Create subnet
openstack subnet create --network <network ID> \​
--subnet-range <subnet CIDR> <subnet name>Create security group
openstack security group create <security group name>
Update subnet
openstack subnet set <key> <value> <subnet ID>
Delete subnet
openstack subnet delete <subnet ID>
Routers
List routers
openstack router list
Show router details
openstack router show <router ID>
Create router
openstack router create <router name>
Update security group
openstack security group set <key> <value> <security group ID>
List rules in the security group
openstack security group rule list <security group ID>
Add rule to the security group
openstack security group rule create \​
<key> <value> ... <security group ID>
Delete rule from the security group
openstack security group rule delete <rule ID>
Delete security group
openstack security group delete <security group ID>
Key pairs
Update router
openstack router set <key> <value> <router ID>List key pairs
openstack keypair list
Attach subnet to router
openstack router add subnet <router ID> <subnet ID>Show key pair details
openstack keypair show <key pair name>
Detach subnet from router
openstack router remove subnet <router ID> <subnet ID>Create key pair
openstack keypair create --private-key <file path> <key pair name>
Delete router
openstack router delete <router ID>Delete key pair
openstack keypair delete <key pair name>


## Quotas
List default quotas
openstack quota show --default
Update default quotas
openstack quota set <key> <value> --class default
Detach floating IP from instance
openstack server remove floating ip <instance ID> <floating IP ID>
Delete floating IP
openstack floating ip delete <floating IP ID>

## Volumes
List project quotas
openstack quota show <project ID>List volumes
openstack volume list
Update project quotas
openstack quota set <key> <value> <project ID>Show volume details
openstack volume show <volume ID>
InstancesCreate volume
openstack volume create --size <size [GB]> <volume name>

## Instances
List instances
openstack server list
Show instance details
openstack server show <instance ID>
Update volume
openstack volume set <key> <value> <volume ID>
Attach the volume to the instance
openstack server add volume <instance ID> <volume ID>
Create instance

openstack server create --flavor <flavor name> 
--image <image ID> --network <network ID> 
--key-name <key pair name> <instance name>Detach the volume from the instance
openstack server remove volume <instance ID> <volume ID>
Update instance
openstack server set <key> <value> <instance ID>openstack volume delete <volume ID>
Delete instance
openstack server delete <instance ID>
Floating IPs
List floating IPs
openstack floating ip list
Create floating IP
openstack floating ip create <network ID>
Attach floating IP to instance
openstack server add floating ip <instance ID> <floating IP ID>
