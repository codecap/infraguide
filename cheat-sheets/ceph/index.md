---
layout: default
title: "Ceph Cheat Sheet: orch, rbd, rados, CephFS | infraguide.org"
description: "Ceph cheat sheet with commands for cephadm orchestration, RBD, RADOS, cephx, CephFS, radosgw and benchmarking. Copy and paste ready."
breadcrumbs:
  - name: cheat-sheets
    url: /cheat-sheets/
  - name: ceph
---
# Ceph Cheat Sheet

Common ceph, rbd and rados commands, grouped by tool. Replace the values to match your cluster.

Want to see these commands in context? Build the [Ceph lab](/learn/ceph/).

## Cluster status & config
```bash
ceph health
ceph health detail

ceph status
ceph -s

ceph df
ceph device ls

# config
ceph config dump
ceph config ls

# mgr modules
ceph mgr module ls
ceph mgr module enable stats

# logs
ceph log last cephadm
tail -f /var/log/ceph/cephadm.log # all nodes
```

### Versions
```bash
ceph versions

ceph osd versions
ceph mon versions
ceph mds versions

ceph tell mon.* version
ceph tell osd.* version
ceph tell mgr.* version
```

## OSDs
```bash
ceph osd tree
ceph osd status
ceph osd info <OSD_NAME>
ceph osd crush class ls
ceph osd dump

# review: check SIZE, USE%, PGS.
ceph osd df
ceph osd df tree
```

### Reweight, out and in
```bash
# change reweight / allocation
# 0 will remove all data from device
ceph osd reweight <OSD_NR> 0.5

# out sets reweight to 0
ceph osd out <OSD_NR>

# in sets reweight to 1
ceph osd in <OSD_NR>

# set the crush weight
ceph osd crush reweight <OSD_NAME> <WEIGHT>

# check, 120 - 20% overload
ceph osd test-reweight-by-pg 120
ceph osd test-reweight-by-utilization 120

# execute
ceph osd reweight-by-pg 120
ceph osd reweight-by-utilization 120

# check again
ceph osd tree
ceph osd df tree
```

### Recovery and flapping flags
```bash
# Disable Recovery
ceph osd set nobackfill
ceph osd set norecover
ceph osd set norebalance

# Enable Recovery
ceph osd unset norebalance
ceph osd unset norecover
ceph osd unset nobackfill

# flapping osds
# prevent OSDs from getting marked up
ceph osd set noup
# prevent OSDs from getting marked down
ceph osd set nodown
ceph osd set noin
ceph osd set noout

ceph osd add-noout <OSD_NAME>
ceph osd rm-noout  <OSD_NAME>
```

### Utilization and full ratios
```bash
ceph df
ceph osd df

# <RATIO> is a float between 0.0 and 1.0
ceph osd set-nearfull-ratio     <RATIO> # OSD_NEARFULL health check
ceph osd set-full-ratio         <RATIO> # clients can not write data
ceph osd set-backfillfull-ratio <RATIO> # backfills will not start
```

### Benchmark OSDs
```bash
# benchmark specific osd
ceph tell <OSD_NAME> bench
ceph --format plain tell <OSD_NAME> bench
# benchmark all osds
ceph tell osd.* bench
ceph --format plain tell osd.* bench
```

## Pools
```bash
ceph osd pool ls
ceph osd pool ls detail
ceph osd pool stats     <POOL>
ceph osd pool get-quota <POOL>
ceph osd pool get noautoscale

ceph osd pool get <POOL> size
ceph osd pool get <POOL> crush_rule
ceph osd pool set ...
ceph osd pool rename ...

ceph osd pool repair <POOL>
ceph osd pool scrub  <POOL>
```

### Create a replicated pool
```bash
# Create a pool for block devices
ceph osd pool create <POOL> 32 32
ceph osd pool application enable <POOL> rbd
ceph osd pool set <POOL> crush_rule <RULE_NAME>
```

### Create an erasure coded pool
```bash
# profile which can sustain loss of 2(m) osds by distributing objects on 5 (3+2) osds totally
# which means 66% overhead only
ceph osd erasure-code-profile set <PROFILE> k=3 m=2 crush-failure-domain=rack
ceph osd erasure-code-profile get <PROFILE>
ceph osd pool create <POOL> 32 erasure <PROFILE>
ceph osd pool application enable <POOL> rgw
ceph osd pool ls detail
```

### Delete a pool
```bash
ceph config set mon mon_allow_pool_delete true
ceph osd pool rm <POOL> <POOL> --yes-i-really-really-mean-it
ceph config set mon mon_allow_pool_delete false
```

## Placement groups
```bash
ceph pg ls
ceph pg ls-by-pool <POOL>
ceph pg ls-by-osd  <OSD_NR>

ceph pg dump_stuck unclean
ceph pg dump_stuck stale

ceph pg dump
ceph pg dump osds
ceph pg dump pools
# brief
ceph pg dump pgs_brief
# summary
ceph pg dump sum
```

## CRUSH & failure domain
```bash
# Bucket types
ceph osd crush dump  | jq .types

# Create racks (repeat per rack)
ceph osd crush add-bucket <RACK_NAME> rack

# Move the racks under the root (repeat per rack)
ceph osd crush move <RACK_NAME> root=default

# Move the hosts into the racks (repeat per host)
ceph osd crush move <HOSTNAME> rack=<RACK_NAME>

# Create a new replicated rule and assign it to a pool
ceph osd crush rule create-replicated <RULE_NAME> default rack
ceph osd crush rule ls
ceph osd crush rule dump <RULE_NAME>
ceph osd pool set <POOL> crush_rule <RULE_NAME>
```

## Ceph orch
```bash
# enable the module
ceph mgr module enable cephadm
ceph -W cephadm

ceph orch ls
ceph orch host ls
ceph orch device ls
ceph orch client-keyring ls

# control cephadm deployment
ceph orch pause
ceph orch resume
```

### Hosts
```bash
# configure hosts to be used within ceph orch
ceph cephadm generate-key
ceph cephadm get-pub-key > ceph.pub
ssh-copy-id -f -i ceph.pub root@<HOSTNAME>

# repeat per host
ceph orch host add <HOSTNAME>
```

### Services and daemons
```bash
# modify / redeploy services
ceph orch apply mds <FS_NAME> --placement=3
ceph orch apply rgw <REALM_NAME> <ZONE_NAME> --placement=3 --port=7480

# apply a spec
ceph orch apply -i <PATH_TO_CEPH_SPEC>

# list running daemons
ceph orch ps
# list running daemons by type
ceph orch ps --daemon-type mds
# restart a single daemon, name from 'ceph orch ps', e.g. mon.cephmon01, osd.3
ceph orch daemon restart <DAEMON_NAME>
# restart all daemons of a service, name from 'ceph orch ls', e.g. rgw.default
ceph orch restart <SERVICE_TYPE>.<SERVICE_ID>

ceph orch ls osd
ceph orch ls osd --export

ceph orch set-managed   <SERVICE_TYPE>.<SERVICE_ID>
ceph orch set-unmanaged <SERVICE_TYPE>.<SERVICE_ID>

# Remove an OSD
ceph orch osd rm --zap <OSD_NR> --force
ceph orch osd rm status

# Remove a ceph service, name from 'ceph orch ls' # TODO: correct ?
ceph orch rm <SERVICE_TYPE>.<SERVICE_ID>
```

### Upgrade
```bash
# list available versions
ceph orch upgrade ls
# start an upgrade to ceph version X.Y.Z
ceph orch upgrade start --ceph-version <CEPH_VERSION> --image quay.io/ceph/ceph:v<CEPH_VERSION>

# will pull quay.io/ceph/ceph:v18.2.4
ceph orch upgrade start --ceph-version <CEPH_VERSION>

# continue to upgrade crash
ceph orch upgrade start --image <REGISTRY>/ceph/ceph:v<CEPH_VERSION> --daemon-types crash

# continue to upgrade osd
ceph orch upgrade start --image <REGISTRY>/ceph/ceph:v<CEPH_VERSION> --daemon-types osd

# get upgrade status
ceph orch upgrade status
ceph orch upgrade stop
```

## Operations

### Replace a Mon Node
```bash
# 🚧 on deployment node remove <HOSTNAME> from the cluster
for l in _admin mds mgr mon nfs rbd-mirror rgw
do
  ceph orch host label rm <HOSTNAME> $l
done
ceph orch host drain <HOSTNAME>
# wait a bit
ceph -s
ceph orch host rm <HOSTNAME>

# 🚧 on <HOSTNAME>
systemctl stop ceph.target
rm -rf  /etc/ceph/ /var/lib/ceph/* /var/log/ceph/* /etc/systemd/system/ceph*
systemctl daemon-reload
docker image ls | awk '{print $3}' | xargs docker rmi

# 🩺 on deployment node check status
ceph -s
ceph orch host ls
ceph orch ls
ceph orch ps

# 🚚 Redeploy
ceph orch apply -i <PATH_TO_CEPH_SPEC>

# 🩺 check status on deployment node
ceph -s
ceph orch ps
```

### Replace an OSD
```bash
# List all the OSDs in the cluster, take the last one, check the devices behind
ceph osd tree
ceph osd metadata <OSD_NR> | grep device

# 🩺 check status
ceph osd tree
ceph status
ceph health detail
ceph device ls

# 🛠️ Tell the cluster not to try to recover
ceph osd set noout
ceph osd set norecover

# 🚧 Rebuild
ceph orch ls osd --export
ceph orch set-unmanaged   osd.<SERVICE_ID>
ceph orch ls osd
ceph osd tree

ceph orch osd rm --zap <OSD_NR> --force
ceph osd purge <OSD_NR> --yes-i-really-mean-it

ceph orch osd rm status
ceph status
ceph health detail

ceph orch daemon rm <OSD_NAME> --force

# 🚧 Set initial weight to 0 to control recovery
ceph config get osd osd_crush_initial_weight
ceph config set osd osd_crush_initial_weight 0

# 🚚 Redeploy
ceph orch set-managed   osd.<SERVICE_ID>
ceph orch ls osd
ceph osd tree
ceph device ls

# 🩺 Check
ceph osd tree
ceph osd df
ceph pg ls-by-osd <OSD_NR>

# 🚚 Allow data distribution again
ceph osd crush reweight <OSD_NAME> <WEIGHT>

ceph config set osd osd_crush_initial_weight -1
ceph config get osd osd_crush_initial_weight
ceph osd unset noout
ceph osd unset norecover
```

### Rebuild an OSD Node
```bash
# Prepare the rebuild
ceph osd set noout
ceph osd set norecover

# 🚧 Drain the Ceph OSD host
ceph orch host drain <HOSTNAME> [--zap-osd-device]
ceph orch host drain status
ceph orch device ls  [<HOSTNAME>]
ceph orch osd  rm --zap <OSD_NR> --force
ceph orch host rm     <HOSTNAME> --force

# Join the cluster again
ceph orch set-managed osd.<SERVICE_ID>
ceph orch apply -i <PATH_TO_CEPH_SPEC>

# Rebuild the host
# ⚠️ Add the ssh key for the ceph deployment in authorized_keys on the host

# 🩺 Check the status
ceph status
ceph health detail

# ⚠️ You may want to control the recovery process – check available options

ceph osd unset noout
ceph osd unset norecover
```

## rados
```bash
# list pools
rados lspools
# list objects in a pool
rados --pool <POOL>  ls
# create an object
rados -p <POOL> put <OBJECT> - <<< 'hello world'
# fetch an object
rados -p <POOL> get <OBJECT> -
```

## rados block device (rbd)
```bash
# create an image
rbd create --image-feature layering --size 1024 <POOL>/<IMAGE>

# list images
rbd list --pool <POOL>
# show info about an image
rbd info <POOL>/<IMAGE>

# resize an image
rbd resize --size 2G <POOL>/<IMAGE>

# Delete an image/volume
rbd rm <POOL>/<IMAGE>
```

### Map and use
```bash
rbd device list
# map an image to a block device
rbd map <POOL>/<IMAGE>
# show mapped images
rbd showmapped

# Use
ls -lh /dev/rbd<N>  /dev/rbd/<POOL>/<IMAGE>
mkfs.ext4 /dev/rbd/<POOL>/<IMAGE>
mount     /dev/rbd/<POOL>/<IMAGE> <MOUNTPOINT>
echo "Hello from Ceph RBD Storage!" > <MOUNTPOINT>/<FILE>
```

### Snapshots
```bash
# create a snapshot
rbd snap create <POOL>/<IMAGE>@<SNAP>
# protect a snapshot
rbd snap protect <POOL>/<IMAGE>@<SNAP>
# unprotect
rbd snap unprotect <POOL>/<IMAGE>@<SNAP>
# list snapshots of an image
rbd snap list --image <POOL>/<IMAGE>
rbd snap ls           <POOL>/<IMAGE>
# map a snapshot to a block device
rbd map <POOL>/<IMAGE>@<SNAP>
# Rollback the image to its snapshot
rbd snap rollback <POOL>/<IMAGE>@<SNAP>
# Delete/Purge all unprotected snapshots.
rbd snap purge <POOL>/<IMAGE>
```

## CephFS
```bash
# Create the metadata pool (needs high-speed disks like SSDs if possible)
ceph osd pool create <METADATA_POOL> 32 32

# Create the data pool (where your files actually sit)
ceph osd pool create <DATA_POOL> 32 32

# Create new filesystem
ceph fs new <FS_NAME> <METADATA_POOL> <DATA_POOL>
ceph fs status

# Create a user with read-write access
ceph auth get-or-create \
  <ENTITY> mon 'allow r' mds 'allow rws' osd 'allow rw pool=<DATA_POOL>' \
  -o /etc/ceph/ceph.<ENTITY>.keyring
ceph auth get <ENTITY>

# Mount
ceph-fuse -n <ENTITY> -k /etc/ceph/ceph.<ENTITY>.keyring <MOUNTPOINT> --client_mds_namespace=<FS_NAME>
```

### MDS
```bash
# show statistics
ceph mds stat

# create an entity to use within mds
ceph auth get-or-create <ENTITY> \
  mds 'allow' mon 'allow r' osd 'allow rw pool=<DATA_POOL>' \
  > /etc/ceph/ceph.<ENTITY>.keyring
ceph auth get-key <ENTITY> > <SECRET_FILE>

# mount CephFS as a Kernel Filesystem (<USER> is <ENTITY> without 'client.')
mount -t ceph <MON_IP>:6789:/ <MOUNTPOINT> -o name=<USER>,secretfile=<SECRET_FILE>

# mount as fuse
ceph-fuse --name <ENTITY> <MOUNTPOINT>
```

### Snapshots
```bash
# Create a working project folder
mkdir -p <MOUNTPOINT>/<DIR>

# Add a sample document
echo "Version 1.0 - Stable Build" > <MOUNTPOINT>/<DIR>/<FILE>

# Take a snapshot (the mkdir trick)
mkdir <MOUNTPOINT>/<DIR>/.snap/<SNAP_NAME>
cat   <MOUNTPOINT>/<DIR>/.snap/<SNAP_NAME>/<FILE>

# Overwrite our working live file
echo "Version 2.0 - Broken Build!" > <MOUNTPOINT>/<DIR>/<FILE>

# Add junk data
echo "Temporary junk" > <MOUNTPOINT>/<DIR>/<OTHER_FILE>

# Restore the file from snapshot
cp <MOUNTPOINT>/<DIR>/.snap/<SNAP_NAME>/<FILE> <MOUNTPOINT>/<DIR>/<FILE>

# NOTE: will not work with rm -rf
rmdir  <MOUNTPOINT>/<DIR>/.snap/<SNAP_NAME>
```

## radosgw
```bash
# list users
radosgw-admin metadata list user
radosgw-admin user list

# create user
radosgw-admin user create --uid=<UID> \
  --display-name="<DISPLAY_NAME>"

# modify user
radosgw-admin user modify --uid=<UID> \
  --display-name="<DISPLAY_NAME>" --email=<EMAIL>

# show user's info / access_key & secret_key
radosgw-admin user info --uid=<UID>

# suspend a user
radosgw-admin user suspend --uid=<UID>
# enable a user
radosgw-admin user enable --uid=<UID>
```

## cephx
```bash
# List all keyrings
ceph auth ls

ceph auth get-or-create <ENTITY> \
  mon 'allow r' \
  osd 'allow rw pool=<POOL>'

# print key of an entity
ceph auth get-key <ENTITY>

ceph auth get <ENTITY>

# remove an entity
ceph auth rm <ENTITY>
```

## ceph-objectstore-tool
```bash
# examining contents of a BlueStore OSD
systemctl stop ceph-osd@<OSD_NR>
ceph-objectstore-tool --op fuse --data-path /var/lib/ceph/osd/ceph-<OSD_NR> --mountpoint <MOUNTPOINT> &
ls <MOUNTPOINT>
systemctl start ceph-osd@<OSD_NR>
```

## Benchmarking

### rados and rbd
```bash
# benchmark on newly created pool + cleanup
ceph osd pool create <POOL> 64
rados bench -p <POOL> 30 write
rados -p <POOL> bench -b 4096 30 write # object size 4096
# read benchmark
rados -p <POOL> bench 30 write --no-cleanup
rados -p <POOL> bench 30 rand
ceph tell mon.\* injectargs '--mon-allow-pool-delete=true'
ceph osd pool delete <POOL> <POOL> --yes-i-really-really-mean-it

# benchmark on a newly created volume + cleanup
rbd create  --size 1024 <POOL>/<IMAGE>
rbd bench-write <POOL>/<IMAGE> --io-size 1048576  --io-threads 16 --io-pattern rand
rbd rm  <POOL>/<IMAGE>

# benchmark on a newly created volume with fio + cleanup
rbd create  --size 1024 <POOL>/<IMAGE>
fio --size=100M --ioengine=rbd --invalidate=0 --direct=1 --numjobs=10 --rw=write --name=fiojob --blocksize_range=4K-512k --iodepth=1 --pool=<POOL> --rbdname=<IMAGE>
rbd rm  <POOL>/<IMAGE>
```

### fio: Sequential Throughput (1MB)
*Simulates: Large file transfers, VM migrations, backups.*
```bash
# dd if=/dev/zero of=testfile bs=1M count=2048 oflag=direct
fio                  \
--name=baseline_seq  \
--ioengine=libaio    \
--direct=1           \
--bs=1M              \
--size=2G            \
--rw=write           \
--iodepth=8          \
--numjobs=1          \
--group_reporting    \
--runtime=60         \
--time_based
```

### fio: Random IOPS (4KB)
*Simulates: General OS responsiveness and small metadata operations.*
```bash
# dd if=testfile of=/dev/null bs=4k count=262144 iflag=direct
fio                  \
--name=baseline_iops \
--ioengine=libaio    \
--direct=1           \
--bs=4k              \
--size=1G            \
--rw=randrw          \
--rwmixread=100      \
--iodepth=32         \
--numjobs=1          \
--group_reporting    \
--runtime=60         \
--time_based
```

### fio: PostgreSQL (8KB Blocks)
```bash
fio                \
--name=db_postgres \
--ioengine=libaio  \
--direct=1         \
--bs=8k            \
--size=2G          \
--rw=randrw        \
--rwmixread=70     \
--iodepth=16       \
--numjobs=2        \
--group_reporting  \
--runtime=120
```

### fio: MySQL/MariaDB (16KB Blocks)
```bash
fio               \
--name=db_mysql   \
--ioengine=libaio \
--direct=1        \
--bs=16k          \
--size=2G         \
--rw=randrw       \
--rwmixread=70    \
--iodepth=16      \
--numjobs=2       \
--group_reporting \
--runtime=120
```
