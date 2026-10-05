---
layout: default
title: Ceph Cheat Sheet: orch, rbd, rados, CephFS | infraguide.org
description: Ceph cheat sheet with commands for cephadm orchestration, RBD, RADOS, cephx, CephFS, radosgw and benchmarking. Copy and paste ready.
breadcrumbs:
  - name: cheat-sheets
    url: /cheat-sheets/
  - name: ceph
---
# Ceph Cheat Sheet

Common ceph, rbd and rados commands, grouped by tool. Replace the values to match your cluster.

## ceph orch
```bash
# enable the module
ceph mgr module enable cephadm

# configure hosts to be user within ceph orch
ceph cephadm generate-key
ceph cephadm get-pub-key > ceph.pub
ssh-copy-id -f -i ceph.pub root@daisy
ceph orch host add daisy
ceph orch host add eric
ceph orch host add frank
ceph orch ps

# upgrade
ceph orch upgrade start --image quay.io/ceph/ceph:v15
# redeploy metadata servers
ceph orch apply mds cephfs --placement=3
# redeploy radosgw servers
ceph orch apply rgw default default --placement=3 --port=7480

# list daemons
ceph orch ps
# list daemons by type
ceph orch ps --daemon-type mds

# start an upgrade
ceph orch upgrade start --ceph-version 16.2.10 --image quay.io/ceph/ceph:v16.2.10
# get update status 
ceph orch upgrade status
````

## rados
```bash
# list pools
rados lspools
#list object in a pool
rados --pool vms  ls
# create an object
rados -p test put testobj - <<< 'hello world'
# fetch an object
rados -p test get testobj -
```
## rados block device (rbd)
```bash
# create an image
rbd create --image-feature layering --size 1024 test/myvol

# list images 
rbd list --pool test
# show info about an image
rbd info test/myvol
# map an image to a block device
rbd map test/myvol
# show mapped images
rbd showmapped
# create a snapshot
rbd snap create test/myvol@snap1
# protect a snaptshot
rbd snap protect test/myvol@snap1
# unprotect
rbd snap unprotect test/myvol@snap1
# list snapshots of an image
rbd snap list --image test/myvol
rbd snap ls           test/myvol
# Delete/Purge all unprotected snapshots.
rbd snap purge test/myvol
# Delete an image/volume
rbd rm test/myvol
# map a snapshot to a block device
rbd map test/myvol@snap1
# Rollback the image to it's snapshot
rbd snap rollback test/myvol@snap1

# resize an image
rbd resize --size 2G test/mycirros
```

## osdmaptool
```bash
# fetch osdmap for testobj
osdmaptool --test-map-object testobj --pool 1 /tmp/osdmap
```

## cephx
```bash
# List all keyrings
ceph auth ls

ceph auth get-or-create client.testuser \
  mon 'allow r' \
  osd 'allow rw pool=test'

# print key of an entity
ceph auth get-key client.testuser

# remove an entity
ceph auth rm client.testuser

```
## MDS
```bash
# show statistics
ceph mds stat

# create an entity to use within mds
ceph auth get-or-create client.cephfs \
  mds 'allow' mon 'allow r' osd 'allow rw pool=cephfs_data' \
  > /etc/ceph/ceph.client.cephfs.keyring
ceph auth get-key client.cephfs > /etc/ceph/cephfs.secret

# mount CephFS as a Kernel Filesystem
mount -t ceph 10.40.20.224:6789:/ /mnt/mycephfs/ -o   name=cephfs,secretfile=/etc/ceph/cephfs.secret

# mount as fuse
ceph-fuse --name client.cephfs /mnt/cephfuse
```

## radosgw
```bash
# list users
radosgw-admin metadata list user

# create user
radosgw-admin user create --uid=JaneWhirlpool \
  --display-name="Jane Whirlpool"

# modify user
radosgw-admin user modify --uid=JaneWhirlpool \
    --display-name="Jane E. Whirlpool" --email=jane.whirlpool@example.com

# show user's info / access_key & secret_key
radosgw-admin user info --uid=JaneWhirlpool

# suspend a user
radosgw-admin user suspend --uid=JaneWhirlpool
# enable a user
radosgw-admin user enable --uid=JaneWhirlpool

```

## ceph-objectstore-tool
```bash
# examining contents of a BlueStore OSD
systemctl stop ceph-osd@0
ceph-objectstore-tool --op fuse --data-path /var/lib/ceph/osd/ceph-0 --mountpoint /mnt &
ls /mnt
systemctl start ceph-osd@0
```


## Benchmarking
```bash
# benchmark specific osd
ceph tell osd.2 bench
ceph --format plain tell osd.0 bench
# benchmark all osds
ceph tell osd.* bench
ceph --format plain tell osd.* bench

# benchmark on newly created pool + cleanup
ceph osd pool create benchmark 64
rados bench -p benchmark 30 write
rados -p benchmark bench -b 4096 30 write # object size 4096
# read benchmartk
rados -p benchmark bench 30 write --no-cleanup
rados -p benchmark bench 30 rand
ceph tell mon.\* injectargs '--mon-allow-pool-delete=true'
ceph osd pool delete benchmark benchmark --yes-i-really-really-mean-it

# bechmark on a newly created volume + cleanup
rbd create  --size 1024 test/benchmark
rbd bench-write test/benchmark --io-size 1048576  --io-threads 16 --io-pattern rand
rbd rm  test/benchmark

# bechmark on a newly created volume + cleanup
rbd create  --size 1024 test/benchmark
fio --size=100M --ioengine=rbd --invalidate=0 --direct=1 --numjobs=10 --rw=write --name=fiojob --blocksize_range=4K-512k --iodepth=1 --pool=test --rbdname=benchmark
rbd rm  test/benchmark

```


## qemu
```bash
# install qemu
yum install -y qemu-kvm qemu-img
# convert an image
qemu-img convert -p -f qcow2 -O raw   cirros.img   rbd:test/cirros
# get image info
qemu-img info                                      rbd:test/cirros
#  start a vm within an image
/usr/libexec/qemu-kvm -m 128 \
  -drive format=raw,file=rbd:test/mycirros,if=virtio,cache=writeback \
  -serial stdio -nographic -monitor none
```
