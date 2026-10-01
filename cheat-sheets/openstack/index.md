---
layout: default
title: OpenStack
breadcrumbs:
  - name: cheat-sheets
    url: /cheat-sheets/
  - name: openstack
---

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
openstack domain show <domain ID>
# create domain
openstack domain create <domain name>
# update domain
openstack domain set <key> <value> <domain ID>
# delete domain
openstack domain delete <domain ID>
```

## Users
```bash
# list users
openstack user list
# show user details
openstack user show <user ID>
# create user
openstack user create --password <password> <user name>
# update user
openstack user set <key> <value> <user ID>
# set user password
openstack user password set
# delete user
openstack user delete <user ID>
```

## Groups
```bash
# list groups
openstack group list
# show group details
openstack group show <group ID>
# create group
openstack group create <group name>
# update group
openstack group set <key> <value> <group ID>
# add user to group
openstack group add user <group ID> <user ID>
# remove user from group
openstack group remove user <group ID> <user ID>
# delete group
openstack group delete <group ID>
```

## Projects
```bash
# list projects
openstack project list
# create project
openstack project create <project name>
# update project
openstack project set <key> <value> <project ID>
# delete project
openstack project delete <project ID>
```

## Flavors
```bash
# list flavors
openstack flavor list
# show flavor details
openstack flavor show <flavor name>
# create flavor
openstack flavor create --vcpus <vCPUs> --ram <RAM [MB]> \
  --disk <Disk [GB]> <flavor name>
# update flavor
openstack flavor set <key> <value> <flavor name>
# delete flavor
openstack flavor delete <flavor name>
```

## Roles
```bash
# assign role on project
openstack role add --project <project ID> \
  [--user <user ID> | --group <group ID>] <role name>
# remove role on project
openstack role remove --project <project ID> \
  [--user <user ID> | --group <group ID>] <role name>
```

## Images
```bash
# list images
openstack image list
# show image details
openstack image show <image ID>
# create image
openstack image create --disk-format qcow2 \
  --file <file path> <image name>
# update image
openstack image set <key> <value> <image ID>
# delete image
openstack image delete <image ID>
```

## Networks
```bash
# list networks
openstack network list
# show network details
openstack network show <network ID>
# create network
openstack network create <network name>
# update network
openstack network set <key> <value> <network ID>
# delete network
openstack network delete <network ID>
```

## Subnets
```bash
# list subnets
openstack subnet list
# show subnet details
openstack subnet show <subnet ID>
# create subnet
openstack subnet create --network <network ID> \
  --subnet-range <subnet CIDR> <subnet name>
# update subnet
openstack subnet set <key> <value> <subnet ID>
# delete subnet
openstack subnet delete <subnet ID>
```

## Security groups
```bash
# list security groups
openstack security group list
# show security group details
openstack security group show <security group ID>
# create security group
openstack security group create <security group name>
# update security group
openstack security group set <key> <value> <security group ID>
# list rules in the security group
openstack security group rule list <security group ID>
# add rule to the security group
openstack security group rule create \
  <key> <value> ... <security group ID>
# delete rule from the security group
openstack security group rule delete <rule ID>
# delete security group
openstack security group delete <security group ID>
```

## Routers
```bash
# list routers
openstack router list
# show router details
openstack router show <router ID>
# create router
openstack router create <router name>
# update router
openstack router set <key> <value> <router ID>
# attach subnet to router
openstack router add subnet <router ID> <subnet ID>
# detach subnet from router
openstack router remove subnet <router ID> <subnet ID>
# delete router
openstack router delete <router ID>
```

## Key pairs
```bash
# list key pairs
openstack keypair list
# show key pair details
openstack keypair show <key pair name>
# create key pair
openstack keypair create --private-key <file path> <key pair name>
# delete key pair
openstack keypair delete <key pair name>
```

## Quotas
```bash
# list default quotas
openstack quota show --default
# update default quotas
openstack quota set <key> <value> --class default
# list project quotas
openstack quota show <project ID>
# update project quotas
openstack quota set <key> <value> <project ID>
```

## Volumes
```bash
# list volumes
openstack volume list
# show volume details
openstack volume show <volume ID>
# create volume
openstack volume create --size <size [GB]> <volume name>
# update volume
openstack volume set <key> <value> <volume ID>
# delete volume
openstack volume delete <volume ID>
```

## Instances
```bash
# list instances
openstack server list
# show instance details
openstack server show <instance ID>
# create instance
openstack server create --flavor <flavor name> \
  --image <image ID> --network <network ID> \
  --key-name <key pair name> <instance name>
# update instance
openstack server set <key> <value> <instance ID>
# attach volume to instance
openstack server add volume <instance ID> <volume ID>
# detach volume from instance
openstack server remove volume <instance ID> <volume ID>
# delete instance
openstack server delete <instance ID>
```

## Floating IPs
```bash
# list floating IPs
openstack floating ip list
# create floating IP
openstack floating ip create <network ID>
# attach floating IP to instance
openstack server add floating ip <instance ID> <floating IP ID>
# detach floating IP from instance
openstack server remove floating ip <instance ID> <floating IP ID>
# delete floating IP
openstack floating ip delete <floating IP ID>
```
