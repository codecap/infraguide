---
layout: default
title: "Ceph Lab: Deploy and Operate a Cluster | infraguide.org"
description: "Free Ceph lab in English: build a cluster from scratch with cephadm, covering block, file and object storage. Workshop slides included."
breadcrumbs:
  - name: learn
    url: /learn/
  - name: ceph
---

<div class="section" style="--topic-tint: var(--color-ceph-tint);">
  <div class="workshop-header">
    <span class="workshop-icon">
      <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/svg/ceph.svg" alt="Ceph logo"/>
    </span>
    <div>
      <h1>Ceph Lab</h1>
      <p>
        Build and operate a Ceph cluster from scratch.
      </p>
    </div>
  </div>
  <p>
    You build a Ceph cluster from scratch with cephadm and run it the way you
    would in production. The lab runs on one bare-metal host with the cluster
    nodes as virtual machines. You work with them like physical servers,
    networking included, and the configuration is the one you would use on
    bare metal. It covers block, file and object storage and shows how Ceph
    serves as the storage backend for OpenStack.
  </p>
  <p>
    After the lab you know the cluster components and how Ceph distributes
    data. You can operate the cluster, replace OSDs and nodes while it is
    running, and read the health signals when something goes wrong.
  </p>
  <ul>
    <li>Architecture: MON, MGR, OSD, CRUSH, pools and placement groups</li>
    <li>Deployment and bootstrap with cephadm</li>
    <li>Block storage (RBD), file system (CephFS) and object storage (S3)</li>
    <li>Ceph as storage backend for OpenStack</li>
    <li>Day two: replacing OSDs and nodes, upgrades, failure domains</li>
    <li>Monitoring, health checks and benchmarking</li>
  </ul>
  <p>
    You need: one bare-metal server with virtualization enabled. The lab
    environment is allocated:
  </p>
  <ul>
    <li>🔲 20 vCPUs</li>
    <li>🗄️ 40 GB RAM</li>
    <li>💽 1 TB disk</li>
  </ul>
  <p>
    Good to know first: Linux, networking, containers and scripting.
  </p>
</div>

<div class="section">
  <h2 class="section-label">Lab</h2>
  <span class="section-sublabel">Environment</span>
  <div class="diagram-frame">
    <img src="{{ '/assets/img/ceph/env.svg' | relative_url }}" alt="Ceph workshop environment diagram"/>
  </div>
  <span class="section-sublabel">Automatic build and state</span>
  {% include build-info.html data=site.data.learn.ceph.build_info %}
  {% include status-panel.html data=site.data.learn.ceph.build_status %}
</div>

<div class="section">
  <h2 class="section-label">On real bare metal</h2>
  <p>
    The same configuration runs on physical servers. The following parts
    differ:
  </p>
  <ul>
    <li>Network interface names, bonding, and the split into public and cluster network</li>
    <li>Disks: device names, device classes and the CRUSH rules for them</li>
    <li>Failure domains that match your real racks and hosts</li>
    <li>Provisioning of the servers: PXE, IPMI or BMC access</li>
  </ul>
</div>

<div class="section">
  <h2 class="section-label">Workshop slides</h2>
  <div class="slides-grid">
    <a class="slide-link slide-link--primary" href="{{ '/learn/ceph/slides.html' | relative_url }}">View slides</a>
    <a class="slide-link" href="{{ '/learn/ceph/slides.pdf' | relative_url }}">Download PDF</a>
    <a class="slide-link slide-link--pptx" href="{{ '/learn/ceph/slides.pptx' | relative_url }}">Download PPTX</a>
    <a class="slide-link slide-link--cheatsheet" href="{{ '/cheat-sheets/ceph/' | relative_url }}">Cheat Sheet</a>
  </div>
  <div class="pill-row">
    <span class="pill">Advanced</span>
    <span class="pill">2 days</span>
    <span class="pill">From scratch</span>
    <span class="pill">Hands-on</span>
    <span class="pill">English</span>
  </div>
  <div>
    <p>
      Want this delivered as a guided workshop? Write to
      <a href="mailto:ping@infraguide.org">ping@infraguide.org</a>.
    </p>
  </div>
</div>
