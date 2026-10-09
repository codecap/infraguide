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

{% include cheat-sheet-notation.md %}

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
ceph osd stat
ceph osd ls
ceph osd info <OSD_NAME>
ceph osd metadata <OSD_NR>
ceph osd perf
ceph osd blocked-by
ceph osd dump
ceph osd dump | grep flags

# Device classes
ceph osd crush class ls
ceph osd crush rm-device-class  <OSD_NAME>
ceph osd crush set-device-class <CLASS> <OSD_NAME>

# review: check SIZE, USE%, PGS.
ceph osd df
ceph osd df tree

# Reweight, out and in
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

# Recovery and flapping flags
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

# pause scrubbing
ceph osd set noscrub
ceph osd set nodeep-scrub
ceph osd unset noscrub
ceph osd unset nodeep-scrub

# pause all client I/O
ceph osd set pause
ceph osd unset pause

ceph osd add-noout <OSD_NAME>
ceph osd rm-noout  <OSD_NAME>

# Utilization and full ratios
ceph df
ceph osd df

# <RATIO> is a float between 0.0 and 1.0
ceph osd set-nearfull-ratio     <RATIO> # OSD_NEARFULL health check
ceph osd set-full-ratio         <RATIO> # clients can not write data
ceph osd set-backfillfull-ratio <RATIO> # backfills will not start

# Benchmark OSDs
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
ceph osd pool application get <POOL>

ceph osd pool get <POOL> all
ceph osd pool get <POOL> size
ceph osd pool get <POOL> crush_rule
ceph osd pool set ...
ceph osd pool rename ...

ceph osd pool repair <POOL>
ceph osd pool scrub  <POOL>

# Quota
ceph osd pool set-quota <POOL> max_bytes   <BYTES>
ceph osd pool set-quota <POOL> max_objects <COUNT>
# 0 removes the quota
ceph osd pool set-quota <POOL> max_bytes 0

# Protect a pool from deletion and from size / pg changes
ceph osd pool set <POOL> nodelete     true
ceph osd pool set <POOL> nosizechange true
ceph osd pool set <POOL> nopgchange   true

# Create a pool for block devices
ceph osd pool create <POOL> 32 32
ceph osd pool application enable <POOL> rbd
ceph osd pool set <POOL> crush_rule <RULE_NAME>

# Create an erasure coded pool
# profile which can sustain loss of 2(m) osds by distributing objects on 5 (3+2) osds totally
# which means 66% overhead only
# FIXME: how to crate the <PROFILE>?
ceph osd erasure-code-profile set <PROFILE> k=3 m=2 crush-failure-domain=rack
ceph osd erasure-code-profile get <PROFILE>
ceph osd pool create <POOL> 32 erasure <PROFILE>
# required to use an EC pool for RBD or CephFS data
ceph osd pool set <POOL> allow_ec_overwrites true
ceph osd pool application enable <POOL> rgw
ceph osd pool ls detail

# Delete a pool
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

## Troubleshooting & health
```bash
# Health warnings
ceph health detail
# silence a warning, <TTL> e.g. 1h, 1d, 1w
ceph health mute   <HEALTH_CODE> <TTL>
# stay muted even if the warning changes or clears and returns
ceph health mute   <HEALTH_CODE> <TTL> --sticky
ceph health unmute <HEALTH_CODE>

# Crashes (daemon crash reports, trigger RECENT_CRASH warning)
ceph crash ls
ceph crash ls-new
ceph crash info <CRASH_ID>
# acknowledge, clears the warning
ceph crash archive <CRASH_ID>
ceph crash archive-all

# Find problematic PGs
ceph pg ls | grep -v active+clean
ceph pg dump_stuck inactive
ceph pg dump_stuck unclean
ceph pg dump_stuck stale

# Inspect a PG, <PG_ID> looks like 2.1f
ceph pg <PG_ID> query
# which OSDs serve the PG
ceph pg map <PG_ID>
# which PG / OSDs hold an object
ceph osd map <POOL> <OBJECT>

# Speed up recovery of a specific PG
ceph pg force-recovery <PG_ID>
ceph pg force-backfill <PG_ID>
ceph pg cancel-force-recovery <PG_ID>
ceph pg cancel-force-backfill <PG_ID>

# Scrub errors (PG_DAMAGED, OSD_SCRUB_ERRORS)
ceph pg scrub      <PG_ID>
ceph pg deep-scrub <PG_ID>
rados list-inconsistent-pg  <POOL>
rados list-inconsistent-obj <PG_ID> --format=json-pretty
ceph pg repair <PG_ID>

# Unfound objects
ceph pg <PG_ID> query | grep unfound
# ⚠️ last resort, data is reverted to a previous version or deleted
ceph pg <PG_ID> mark_unfound_lost revert
ceph pg <PG_ID> mark_unfound_lost delete

# ⚠️ declare a dead OSD lost, only if it will never come back
ceph osd lost <OSD_NR> --yes-i-really-mean-it

# Slow requests and daemon logs
ceph osd perf
journalctl -u "ceph*" | grep "slow request"
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
# rule for a device class, e.g. ssd or hdd
ceph osd crush rule create-replicated <RULE_NAME> default host <CLASS>
# rule for an erasure coded pool
ceph osd crush rule create-erasure <RULE_NAME> <PROFILE>
ceph osd crush rule rm <RULE_NAME>

# Show the tree, --show-shadow adds the per-class trees
ceph osd crush tree
ceph osd crush tree --show-shadow

# Change the hierarchy
# link a bucket under a second parent / remove that link
ceph osd crush link   <BUCKET> <TYPE>=<PARENT>
ceph osd crush unlink <BUCKET> <PARENT>
# set the crush weight and location of an OSD
ceph osd crush set <OSD_NAME> <WEIGHT> <TYPE>=<BUCKET>
# remove a bucket or an OSD from the map
ceph osd crush remove <NAME>

# Tunables
ceph osd crush show-tunables
# <PROFILE> is optimal, default or a release name, data movement on change
ceph osd crush tunables <PROFILE>
```

### Edit the CRUSH map by hand
```bash
# Get and decompile
ceph osd getcrushmap -o <CRUSHMAP_BIN>
crushtool -d <CRUSHMAP_BIN> -o <CRUSHMAP_TXT>

# Edit rules, buckets or tunables
vi <CRUSHMAP_TXT>

# Compile
crushtool -c <CRUSHMAP_TXT> -o <CRUSHMAP_NEW_BIN>

# Test before injecting: mappings and utilization of a rule
crushtool -i <CRUSHMAP_NEW_BIN> --test --rule <RULE_ID> --num-rep <SIZE> \
  --show-mappings
crushtool -i <CRUSHMAP_NEW_BIN> --test --rule <RULE_ID> --num-rep <SIZE> \
  --show-utilization
# inputs that CRUSH fails to map to <SIZE> OSDs
crushtool -i <CRUSHMAP_NEW_BIN> --test --rule <RULE_ID> --num-rep <SIZE> \
  --show-bad-mappings

# ⚠️ Inject, triggers data movement, keep <CRUSHMAP_BIN> as a way back
ceph osd setcrushmap -i <CRUSHMAP_NEW_BIN>
```

## Balancer & autoscaler
```bash
# Balancer: evens out PGs across OSDs
ceph balancer status
ceph balancer on
ceph balancer off

# upmap needs luminous+ clients
ceph osd set-require-min-compat-client luminous
ceph balancer mode upmap
# alternatives: crush-compat, none

# score the current distribution, lower is better
ceph balancer eval
ceph balancer eval <POOL>

# manual run: create a plan, review it, execute it
ceph balancer optimize <PLAN_NAME>
ceph balancer show     <PLAN_NAME>
ceph balancer eval     <PLAN_NAME>
ceph balancer execute  <PLAN_NAME>
ceph balancer rm       <PLAN_NAME>

# limit how much data moves at once, fraction of PGs (default 0.05)
ceph config set mgr target_max_misplaced_ratio 0.05

# lower the share of primaries on an OSD, <AFFINITY> is between 0.0 and 1.0
ceph osd primary-affinity <OSD_NR> <AFFINITY>

# PG autoscaler: sizes pg_num per pool
ceph osd pool autoscale-status

# <MODE> is on, off or warn
ceph osd pool set <POOL> pg_autoscale_mode <MODE>
# default mode for new pools
ceph config set global osd_pool_default_pg_autoscale_mode <MODE>

# pause / resume autoscaling for the whole cluster
ceph osd pool set noautoscale
ceph osd pool unset noautoscale
ceph osd pool get noautoscale

# hints: expected share of the cluster capacity, <RATIO> between 0.0 and 1.0
ceph osd pool set <POOL> target_size_ratio <RATIO>
# start with the final number of PGs, avoids early data movement
ceph osd pool set <POOL> bulk true
# never go below this many PGs
ceph osd pool set <POOL> pg_num_min <N>
```

## Configuration & tuning
```bash
# Central config database
ceph config dump
# <WHO> is global, mon, osd, mds, mgr, client or a daemon like osd.3
ceph config get <WHO> <KEY>
ceph config set <WHO> <KEY> <VALUE>
# back to the default
ceph config rm  <WHO> <KEY>
# effective values of a running daemon, with source (default, mon, file, override)
ceph config show osd.<ID>
ceph config show-with-defaults osd.<ID>
# help and default of an option
ceph config help <KEY>

# Import an old ceph.conf into the config database
ceph config assimilate-conf -i /etc/ceph/ceph.conf
# minimal ceph.conf for clients
ceph config generate-minimal-conf

# Runtime override, not persistent
ceph tell osd.<ID> config set <KEY> <VALUE>
ceph tell osd.* injectargs '--osd_max_backfills=2'
ceph tell mon.* injectargs '--mon_allow_pool_delete=true'
# via the admin socket on the daemon's host
ceph daemon osd.<ID> config get <KEY>

# Key/value store of the mons, used by mgr modules and the orchestrator
ceph config-key ls
ceph config-key get <KEY>
ceph config-key set <KEY> <VALUE>

# Recovery and backfill speed vs client I/O
ceph config set osd osd_max_backfills 1
ceph config set osd osd_recovery_max_active 3
ceph config set osd osd_recovery_op_priority 3
# mclock (default since Quincy): pick a profile instead of single options
# <PROFILE> is balanced, high_client_ops or high_recovery_ops
ceph config set osd osd_mclock_profile <PROFILE>
# allow manual backfill / recovery options with mclock
ceph config set osd osd_mclock_override_recovery_settings true

# Scrub window and intervals
ceph config set osd osd_scrub_begin_hour <0-23>
ceph config set osd osd_scrub_end_hour   <0-23>
ceph config set osd osd_scrub_min_interval <SECONDS>
ceph config set osd osd_deep_scrub_interval <SECONDS>

# OSD memory
ceph config set osd osd_memory_target <BYTES>
```

## Monitoring & telemetry
```bash
# Progress of recovery, rebalancing and other long-running events
ceph progress
ceph progress json

# Recent cluster log, <CHANNEL> is cluster or audit
ceph log last <COUNT> info <CHANNEL>

# Deploy the monitoring stack with the orchestrator
ceph orch apply prometheus
ceph orch apply grafana
ceph orch apply alertmanager
ceph orch apply node-exporter
ceph orch ls --service-type prometheus

# Prometheus metrics endpoint of the active mgr, port 9283
ceph mgr module enable prometheus
ceph mgr services
curl -s http://<MGR_HOST>:9283/metrics | head

# Dashboard
ceph mgr module enable dashboard
ceph dashboard create-self-signed-cert
ceph dashboard ac-user-create <USER> -i <PASSWORD_FILE> administrator
ceph dashboard ac-user-show
ceph dashboard ac-user-delete <USER>
ceph dashboard set-grafana-api-url http://<GRAFANA_HOST>:3000
ceph dashboard set-alertmanager-api-host http://<ALERTMANAGER_HOST>:9093

# Telemetry: anonymous usage reports sent to the Ceph project
ceph telemetry show
ceph telemetry status
ceph telemetry on --license sharing-1-0
ceph telemetry off
```

## Ceph orch

### cephadm
```bash
# Bootstrap the first node
cephadm bootstrap --mon-ip <MON_IP>
cephadm bootstrap --mon-ip <MON_IP> \
  --public-network <CIDR> --cluster-network <CIDR>
# pin the container image
cephadm bootstrap --mon-ip <MON_IP> --image quay.io/ceph/ceph:v<CEPH_VERSION>
# no dashboard / monitoring stack
cephadm bootstrap --mon-ip <MON_IP> --skip-dashboard --skip-monitoring-stack
# apply a service spec right away
cephadm bootstrap --mon-ip <MON_IP> --apply-spec <PATH_TO_CEPH_SPEC>

# Shell with the ceph CLI and keyrings inside a container
cephadm shell
cephadm shell -- ceph -s

# Daemons running on this host, and their logs
cephadm ls
cephadm logs --name <DAEMON_NAME>
# run ceph-volume in the container
cephadm ceph-volume -- lvm list

# Check the host before adding it to the cluster
ceph cephadm check-host <HOSTNAME>
```

### orchestrator
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

# Hosts
# configure hosts to be used within ceph orch
ceph cephadm generate-key
ceph cephadm get-pub-key > ceph.pub
ssh-copy-id -f -i ceph.pub root@<HOSTNAME>

# repeat per host
ceph orch host add <HOSTNAME>
# with an IP and labels, _admin also copies ceph.conf and the admin keyring
ceph orch host add <HOSTNAME> <IP> --labels _admin,mon,osd
ceph orch host label add <HOSTNAME> <LABEL>
ceph orch host label rm  <HOSTNAME> <LABEL>
ceph orch host ls --host-pattern <HOSTNAME>
ceph orch host set-addr <HOSTNAME> <NEW_IP>

# Maintenance mode, stops all daemons of the host and sets noout for it
ceph orch host maintenance enter <HOSTNAME>
ceph orch host maintenance exit  <HOSTNAME>

# Remove a host: drain moves all daemons away first
ceph orch host drain <HOSTNAME>
ceph orch osd rm status
ceph orch host rm <HOSTNAME>

# Services and daemons
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

ceph orch daemon stop  <DAEMON_NAME>
ceph orch daemon start <DAEMON_NAME>
# redeploy a daemon or a whole service, e.g. after changing the image
ceph orch daemon redeploy <DAEMON_NAME>
ceph orch redeploy <SERVICE_TYPE>.<SERVICE_ID>
# add a single daemon on a host
ceph orch daemon add mon <HOSTNAME>
ceph orch daemon add mgr <HOSTNAME>

ceph orch ls osd
ceph orch ls osd --export

# Create OSDs
ceph orch device ls --refresh
# preview first
ceph orch apply osd --all-available-devices --dry-run
ceph orch apply osd --all-available-devices
# a single device
ceph orch daemon add osd <HOSTNAME>:<DEVICE_PATH>
# wipe a device so it can be used again
ceph orch device zap <HOSTNAME> <DEVICE_PATH> --force

ceph orch set-managed   <SERVICE_TYPE>.<SERVICE_ID>
ceph orch set-unmanaged <SERVICE_TYPE>.<SERVICE_ID>

# Remove an OSD
ceph orch osd rm --zap <OSD_NR> --force
ceph orch osd rm status

# Remove a ceph service, name from 'ceph orch ls' # TODO: correct ?
ceph orch rm <SERVICE_TYPE>.<SERVICE_ID>

# Upgrade
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

# check an image before upgrading
ceph orch upgrade check --image <REGISTRY>/ceph/ceph:v<CEPH_VERSION>

# get upgrade status
ceph orch upgrade status
ceph orch upgrade pause
ceph orch upgrade resume
ceph orch upgrade stop
```

## Operations

### Pre-upgrade backups & checks
```bash
# 💾 Back up the cluster maps and config
mkdir -p <BACKUP_DIR>
ceph mon getmap -o <BACKUP_DIR>/monmap.bin
ceph osd getmap -o <BACKUP_DIR>/osdmap.bin
ceph osd getcrushmap -o <BACKUP_DIR>/crushmap.bin
ceph auth export -o <BACKUP_DIR>/ceph.auth.export
ceph config dump > <BACKUP_DIR>/ceph-config.txt
ceph orch ls --export > <BACKUP_DIR>/ceph-spec.yaml
ceph osd dump > <BACKUP_DIR>/osd-dump.txt

# 🩺 Cluster must be healthy
ceph -s
ceph health detail
ceph versions

# 🩺 Is it safe to stop these daemons? Use before restarting or removing them
ceph osd ok-to-stop <OSD_NR> [<OSD_NR> ...]
ceph mon ok-to-stop <MON_NAME>
ceph mds ok-to-stop <MDS_NAME>
ceph orch host ok-to-stop <HOSTNAME>

# 🩺 Is it safe to destroy this OSD? Use before purging or replacing it
ceph osd safe-to-destroy <OSD_NR>

# 🛠️ Optional: avoid rebalancing during the upgrade
ceph osd set noout
ceph osd set norebalance
# upgrade
ceph osd unset norebalance
ceph osd unset noout
```

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

## Rados
```bash
# list pools
rados lspools
# list objects in a pool
rados --pool <POOL>  ls
# create an object
rados -p <POOL> put <OBJECT> - <<< 'hello world'
# fetch an object
rados -p <POOL> get <OBJECT> -
# size and mtime
rados -p <POOL> stat <OBJECT>
rados -p <POOL> rm <OBJECT>
rados -p <POOL> cp <OBJECT> <NEW_OBJECT>
# per pool usage
rados df

# Namespaces, --all lists every namespace
rados -p <POOL> ls --all
rados -p <POOL> -N <NAMESPACE> ls

# Extended attributes
rados -p <POOL> listxattr <OBJECT>
rados -p <POOL> getxattr <OBJECT> <KEY>
rados -p <POOL> setxattr <OBJECT> <KEY> <VALUE>
rados -p <POOL> rmxattr  <OBJECT> <KEY>

# Omap (key/value data of an object)
rados -p <POOL> listomapkeys <OBJECT>
rados -p <POOL> listomapvals <OBJECT>
rados -p <POOL> getomapval <OBJECT> <KEY>
rados -p <POOL> setomapval <OBJECT> <KEY> <VALUE>
rados -p <POOL> rmomapkey  <OBJECT> <KEY>

# Copy all objects to another pool, which must exist. Snapshots are not copied
rados cppool <SRC_POOL> <DST_POOL>
# export a pool to a file or directory, and import it into a pool
rados export --create <POOL> <FILE>
rados import <FILE> <POOL>

# Rados block device (rbd)
# create an image
rbd create --image-feature layering --size 1024 <POOL>/<IMAGE>

# list images
rbd list --pool <POOL>
# show info about an image
rbd info <POOL>/<IMAGE>
# provisioned vs. used space
rbd du <POOL>/<IMAGE>
# who is using the image (watchers)
rbd status <POOL>/<IMAGE>

# resize an image
rbd resize --size 2G <POOL>/<IMAGE>
# shrinking needs a flag, may destroy data
rbd resize --size 1G <POOL>/<IMAGE> --allow-shrink

# enable / disable image features
rbd feature enable  <POOL>/<IMAGE> <FEATURE>
rbd feature disable <POOL>/<IMAGE> <FEATURE>

# Delete an image/volume
rbd rm <POOL>/<IMAGE>

# Trash: delete with a way back
rbd trash mv <POOL>/<IMAGE>
rbd trash ls <POOL>
rbd trash restore <POOL> <IMAGE_ID>
rbd trash rm      <POOL> <IMAGE_ID>
rbd trash purge   <POOL>

# Map and use
rbd device list
# map an image to a block device
rbd map <POOL>/<IMAGE>
# show mapped images
rbd showmapped
# unmap, by device or by image
rbd unmap /dev/rbd<N>
rbd unmap <POOL>/<IMAGE>

# Use
ls -lh /dev/rbd<N>  /dev/rbd/<POOL>/<IMAGE>
mkfs.ext4 /dev/rbd/<POOL>/<IMAGE>
mount     /dev/rbd/<POOL>/<IMAGE> <MOUNTPOINT>
echo "Hello from Ceph RBD Storage!" > <MOUNTPOINT>/<FILE>

# Snapshots
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
# Delete a snapshot
rbd snap rm <POOL>/<IMAGE>@<SNAP>
# Delete/Purge all unprotected snapshots.
rbd snap purge <POOL>/<IMAGE>

# Clones
# clone from a protected snapshot
rbd clone <POOL>/<IMAGE>@<SNAP> <POOL>/<CLONE>
# list clones of a snapshot
rbd children <POOL>/<IMAGE>@<SNAP>
# detach the clone from its parent, copies all data
rbd flatten <POOL>/<CLONE>

# Export and import
rbd export <POOL>/<IMAGE> <FILE>
rbd import <FILE> <POOL>/<IMAGE>
# incremental: changes between two snapshots
rbd export-diff <POOL>/<IMAGE>@<SNAP2> --from-snap <SNAP1> <DIFF_FILE>
rbd import-diff <DIFF_FILE> <POOL>/<IMAGE>

# Changes of an image since a snapshot, and real usage
rbd diff <POOL>/<IMAGE> --from-snap <SNAP>
rbd du <POOL>/<IMAGE>
# free blocks that only contain zeros
rbd sparsify <POOL>/<IMAGE>

# Consistency groups: snapshot several images at once
rbd group create <POOL>/<GROUP>
rbd group image add <POOL>/<GROUP> <POOL>/<IMAGE>
rbd group image list <POOL>/<GROUP>
rbd group snap create <POOL>/<GROUP>@<SNAP>
rbd group snap list   <POOL>/<GROUP>
rbd group snap rollback <POOL>/<GROUP>@<SNAP>
rbd group rm <POOL>/<GROUP>

# Map with the userspace client (no kernel module needed)
rbd-nbd map <POOL>/<IMAGE>
rbd-nbd list-mapped
rbd-nbd unmap /dev/nbd<N>

# Mirroring between two clusters
# <MODE> is pool (all images) or image (enable per image)
rbd mirror pool enable <POOL> <MODE>
rbd mirror pool info   <POOL>
rbd mirror pool status <POOL> --verbose
# snapshot based, journal is the alternative
rbd mirror image enable  <POOL>/<IMAGE> snapshot
rbd mirror image status  <POOL>/<IMAGE>
# failover: demote on the old primary, promote on the new one
rbd mirror image demote  <POOL>/<IMAGE>
rbd mirror image promote <POOL>/<IMAGE>
# old primary is down: promote with --force, then resync the old one after it is back
rbd mirror image promote <POOL>/<IMAGE> --force
rbd mirror image resync  <POOL>/<IMAGE>
# deploy the mirror daemon
ceph orch apply rbd-mirror --placement=<COUNT>

# Performance
rbd perf image iostat
rbd perf image stats <POOL>

# Map images at boot (rbdmap service)
echo "<POOL>/<IMAGE> id=<USER>,keyring=/etc/ceph/ceph.client.<USER>.keyring" >> /etc/ceph/rbdmap
systemctl enable --now rbdmap
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

# mount CephFS as a Kernel Filesystem (<USER> is <ENTITY> without 'client.')
mount -t ceph <MON_IP>:6789:/ <MOUNTPOINT> -o name=<USER>,secretfile=/etc/ceph/ceph.<ENTITY>.keyring

# show statistics
ceph mds stat

# Settings
ceph fs get <FS_NAME>
# number of active MDS daemons, the rest become standby
ceph fs set <FS_NAME> max_mds <N>
ceph fs set <FS_NAME> allow_standby_replay true
ceph fs set <FS_NAME> standby_count_wanted <N>

# Clients
ceph tell mds.<MDS_NAME> session ls
ceph tell mds.<MDS_NAME> session evict id=<CLIENT_ID>

# Failover and recovery
# fail an MDS rank, a standby takes over
ceph mds fail <MDS_NAME>
# take the filesystem offline, and back online
ceph fs fail <FS_NAME>
ceph fs set  <FS_NAME> joinable true

# Scrub
ceph tell mds.<FS_NAME>:0 scrub start / recursive
ceph tell mds.<FS_NAME>:0 scrub status

# Pin a directory to an MDS rank
setfattr -n ceph.dir.pin -v <RANK> <MOUNTPOINT>/<DIR>

# Quota on a directory, 0 removes it
setfattr -n ceph.quota.max_bytes -v <BYTES> <MOUNTPOINT>/<DIR>
setfattr -n ceph.quota.max_files -v <COUNT> <MOUNTPOINT>/<DIR>
getfattr -n ceph.quota.max_bytes <MOUNTPOINT>/<DIR>
getfattr -n ceph.quota.max_files <MOUNTPOINT>/<DIR>

# Subvolumes: managed directories with quota and snapshots
ceph fs subvolumegroup create <FS_NAME> <GROUP>
ceph fs subvolumegroup ls     <FS_NAME>
ceph fs subvolume create <FS_NAME> <SUBVOLUME> --group_name <GROUP> --size <BYTES>
ceph fs subvolume ls     <FS_NAME> --group_name <GROUP>
ceph fs subvolume info   <FS_NAME> <SUBVOLUME> --group_name <GROUP>
# path to use in the mount
ceph fs subvolume getpath <FS_NAME> <SUBVOLUME> --group_name <GROUP>
ceph fs subvolume resize  <FS_NAME> <SUBVOLUME> <BYTES> --group_name <GROUP>
ceph fs subvolume snapshot create <FS_NAME> <SUBVOLUME> <SNAP> --group_name <GROUP>
ceph fs subvolume snapshot ls     <FS_NAME> <SUBVOLUME> --group_name <GROUP>
ceph fs subvolume rm <FS_NAME> <SUBVOLUME> --group_name <GROUP>

# Scheduled snapshots
ceph mgr module enable snap_schedule
ceph fs snap-schedule add <PATH> <INTERVAL> --fs <FS_NAME>
# keep 24 hourly and 7 daily snapshots
ceph fs snap-schedule retention add <PATH> h 24 --fs <FS_NAME>
ceph fs snap-schedule retention add <PATH> d 7  --fs <FS_NAME>
ceph fs snap-schedule status <PATH> --fs <FS_NAME>
ceph fs snap-schedule list   <PATH> --fs <FS_NAME>
ceph fs snap-schedule remove <PATH> <INTERVAL> --fs <FS_NAME>


# Snapshots
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

## NFS
```bash
# Deploy an NFS cluster (Ganesha) on the given hosts
ceph nfs cluster create <CLUSTER_ID> "<HOST1>,<HOST2>"
ceph nfs cluster ls
ceph nfs cluster info <CLUSTER_ID>
ceph nfs cluster rm <CLUSTER_ID>

# Export a CephFS directory
ceph nfs export create cephfs --cluster-id <CLUSTER_ID> \
  --pseudo-path /<EXPORT> --fsname <FS_NAME> --path /<DIR>
# read-only
ceph nfs export create cephfs --cluster-id <CLUSTER_ID> \
  --pseudo-path /<EXPORT> --fsname <FS_NAME> --path /<DIR> --readonly

# Export an RGW bucket
ceph nfs export create rgw --cluster-id <CLUSTER_ID> \
  --pseudo-path /<EXPORT> --bucket <BUCKET>

ceph nfs export ls   <CLUSTER_ID>
ceph nfs export info <CLUSTER_ID> /<EXPORT>
# change an export from a JSON spec, 'export info' prints the current one
ceph nfs export apply <CLUSTER_ID> -i <EXPORT_JSON>
ceph nfs export rm <CLUSTER_ID> /<EXPORT>

# Mount on a client
mount -t nfs -o vers=4.1 <NFS_HOST>:/<EXPORT> <MOUNTPOINT>
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

# remove a user, --purge-data also deletes their buckets and objects
radosgw-admin user rm --uid=<UID> --purge-data

# S3 keys
radosgw-admin key create --uid=<UID> --key-type=s3
radosgw-admin key rm --uid=<UID> --access-key=<ACCESS_KEY>

# Swift subuser and key
radosgw-admin subuser create --uid=<UID> --subuser=<UID>:<SUBUSER> --access=full
radosgw-admin key create --uid=<UID> --subuser=<UID>:<SUBUSER> --key-type=swift

# Buckets
radosgw-admin bucket list
radosgw-admin bucket list --uid=<UID>
# size and object count, all buckets without --bucket
radosgw-admin bucket stats --bucket=<BUCKET>
# check the index, --fix repairs it
radosgw-admin bucket check --bucket=<BUCKET>
# move a bucket to another user
radosgw-admin bucket unlink --uid=<UID> --bucket=<BUCKET>
radosgw-admin bucket link   --uid=<NEW_UID> --bucket=<BUCKET>
# delete a bucket including its objects
radosgw-admin bucket rm --bucket=<BUCKET> --purge-objects

# Quota, <SCOPE> is user or bucket
radosgw-admin quota set --quota-scope=<SCOPE> --uid=<UID> \
  --max-size=<SIZE> --max-objects=<COUNT>
radosgw-admin quota enable  --quota-scope=<SCOPE> --uid=<UID>
radosgw-admin quota disable --quota-scope=<SCOPE> --uid=<UID>

# Usage (needs rgw_enable_usage_log = true)
radosgw-admin usage show --uid=<UID> --start-date=<YYYY-MM-DD> --end-date=<YYYY-MM-DD>
radosgw-admin usage trim --uid=<UID>

# Garbage collection and lifecycle
radosgw-admin gc list --include-all
radosgw-admin gc process
radosgw-admin lc list
radosgw-admin lc process
radosgw-admin lc get --bucket=<BUCKET>

# Realm, zonegroup and zone (multisite)
radosgw-admin realm list
radosgw-admin zonegroup list
radosgw-admin zone list
radosgw-admin realm create --rgw-realm=<REALM> --default
radosgw-admin zonegroup create --rgw-zonegroup=<ZONEGROUP> --rgw-realm=<REALM> \
  --master --default
radosgw-admin zone create --rgw-zonegroup=<ZONEGROUP> --rgw-zone=<ZONE> \
  --master --default --endpoints=http://<RGW_HOST>:<PORT>
radosgw-admin zone get --rgw-zone=<ZONE>
# apply realm / zone changes
radosgw-admin period update --commit

# Multisite sync
radosgw-admin sync status
radosgw-admin sync error list
radosgw-admin sync error trim

# Deploy the gateway with the orchestrator
ceph orch apply rgw <SERVICE_ID> --realm=<REALM> --zone=<ZONE> \
  --placement="<COUNT> <HOST1> <HOST2>"
ceph orch ls --service-type rgw
```

### S3 client
```bash
# Install and configure awscli
pip3 install awscli awscli-plugin-endpoint

mkdir ~/.aws
cat > ~/.aws/config <<EOF
[plugins]
endpoint = awscli_plugin_endpoint
[profile default]
s3 =
  endpoint_url = http://<RGW_HOST>
  signature_version = s3v4
  addressing_style = auto
s3api =
  endpoint_url = http://<RGW_HOST>
EOF

cat > ~/.aws/credentials <<EOF
[default]
aws_access_key_id = <ACCESS_KEY>
aws_secret_access_key = <SECRET_KEY>
EOF

# Create a new bucket
aws s3 mb s3://<BUCKET>

# Upload a file
echo "This is object data stored in Ceph RGW." > test.txt
aws s3 cp test.txt s3://<BUCKET>/<PREFIX>/test.txt

# Review
aws s3 ls s3://<BUCKET>/
aws s3 ls s3://<BUCKET>/<PREFIX>/

# Download
aws s3 cp s3://<BUCKET>/<PREFIX>/test.txt /tmp/test.txt
cat /tmp/test.txt

# Sync a directory
aws s3 sync <DIR> s3://<BUCKET>/
```

### s3cmd
```bash
# interactive setup, writes ~/.s3cfg
s3cmd --configure
# then set in ~/.s3cfg:
#   host_base   = <RGW_HOST>
#   host_bucket = <RGW_HOST>
#   use_https   = False

s3cmd mb s3://<BUCKET>
s3cmd ls
s3cmd put <FILE> s3://<BUCKET>/<PREFIX>/
s3cmd ls s3://<BUCKET>/<PREFIX>/
s3cmd get s3://<BUCKET>/<PREFIX>/<FILE> <DIR>/
s3cmd sync <DIR>/ s3://<BUCKET>/
s3cmd rm s3://<BUCKET>/<PREFIX>/<FILE>
# remove a bucket, --recursive --force empties it first
s3cmd rb s3://<BUCKET>
```

### mc (MinIO client)
```bash
# Install, add --proxy <PROXY_HOST>:<PORT> to curl if needed
sudo curl -o /usr/local/bin/mc -L \
  https://dl.min.io/client/mc/release/linux-amd64/mc
sudo chmod 755 /usr/local/bin/mc

# Configure
mc alias set <ALIAS> http://<RGW_HOST> <ACCESS_KEY> <SECRET_KEY>
mc alias ls
mc --autocompletion
source ~/.bashrc

# Test
mc ls  <ALIAS>
mc cat <ALIAS>/<BUCKET>/<PREFIX>/test.txt
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

# change the caps of an entity, replaces all existing caps
ceph auth caps <ENTITY> \
  mon 'allow r' \
  osd 'allow rw pool=<POOL>'

# profiles for common roles
ceph auth get-or-create client.<NAME> \
  mon 'profile rbd' \
  osd 'profile rbd pool=<POOL>' \
  mgr 'profile rbd pool=<POOL>'

# CephFS client limited to a directory
ceph fs authorize <FS_NAME> client.<NAME> /<DIR> rw

# export to / import from a keyring file
ceph auth get <ENTITY> -o /etc/ceph/ceph.<ENTITY>.keyring
ceph auth import -i /etc/ceph/ceph.<ENTITY>.keyring
ceph auth export > <BACKUP_DIR>/ceph.auth

# rotate the key of an entity (newer releases only)
ceph auth rotate <ENTITY>

# create a keyring offline
ceph-authtool --create-keyring <KEYRING> --gen-key -n <ENTITY>
ceph-authtool <KEYRING> -n <ENTITY> --cap mon 'allow r' --cap osd 'allow rw pool=<POOL>'
ceph-authtool --print-key <KEYRING>

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

# The OSD must be stopped. With cephadm, stop it and open a shell with its data path:
ceph orch daemon stop osd.<OSD_NR>
cephadm shell --name osd.<OSD_NR>

# list the PGs on an OSD
ceph-objectstore-tool --data-path /var/lib/ceph/osd/ceph-<OSD_NR> --op list-pgs

# export a PG, e.g. to rescue it from a failing OSD
ceph-objectstore-tool --data-path /var/lib/ceph/osd/ceph-<OSD_NR> \
  --pgid <PG_ID> --op export --file <FILE>
# import into another stopped OSD
ceph-objectstore-tool --data-path /var/lib/ceph/osd/ceph-<OSD_NR> \
  --op import --file <FILE>
# remove a PG copy from this OSD
ceph-objectstore-tool --data-path /var/lib/ceph/osd/ceph-<OSD_NR> \
  --pgid <PG_ID> --op remove --force
```

## ceph-bluestore-tool
```bash
# Run with the OSD stopped, see ceph-objectstore-tool above for cephadm
# labels of the device
ceph-bluestore-tool show-label --dev /dev/<DEVICE>

# check the OSD's store, repair fixes what fsck finds
ceph-bluestore-tool fsck   --path /var/lib/ceph/osd/ceph-<OSD_NR>
ceph-bluestore-tool repair --path /var/lib/ceph/osd/ceph-<OSD_NR>

# sizes of the data, db and wal devices
ceph-bluestore-tool bluefs-bdev-sizes --path /var/lib/ceph/osd/ceph-<OSD_NR>
# use the full size after the underlying device (LV, disk) was grown
ceph-bluestore-tool bluefs-bdev-expand --path /var/lib/ceph/osd/ceph-<OSD_NR>

# recreate the OSD's data directory from the device labels
ceph-bluestore-tool prime-osd-dir --dev /dev/<DEVICE> \
  --path /var/lib/ceph/osd/ceph-<OSD_NR>
```

## Disaster recovery
```bash
# Mon map
# fetch it from the cluster
ceph mon getmap -o <FILE>
# from a stopped mon
ceph-mon -i <MON_ID> --extract-monmap <FILE>
monmaptool --print <FILE>
# remove a dead mon from the map, then inject it into the surviving mon (stopped)
monmaptool --rm <DEAD_MON_ID> <FILE>
ceph-mon -i <MON_ID> --inject-monmap <FILE>

# Disk health and identification
ceph device ls
ceph device get-health-metrics <DEVICE_ID>
ceph device predict-health <DEVICE_ID>
# blink the locate LED, on / off
ceph device light on  <DEVICE_ID> ident
ceph device light off <DEVICE_ID> ident

# Release and client compatibility gates, after all daemons are upgraded
ceph osd require-osd-release <RELEASE>
ceph osd set-require-min-compat-client <RELEASE>
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
