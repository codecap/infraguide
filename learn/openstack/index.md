---
layout: default
title: "OpenStack Lab: Bare Metal to Private Cloud | infraguide.org"
description: "Free OpenStack lab in English: deploy and operate a multi-node private cloud with kolla-ansible and Ceph. Workshop slides included."
breadcrumbs:
  - name: learn
    url: /learn/
  - name: openstack
---

<div class="section" style="--topic-tint: var(--color-openstack-tint);">
  <div class="workshop-header">
    <span class="workshop-icon">
      <img src="{{ '/assets/img/logos/openstack.svg' | relative_url }}" alt="OpenStack logo"/>
    </span>
    <div>
      <h1>OpenStack Lab</h1>
      <p>
        Deploy and operate a multi-node OpenStack cloud.
      </p>
    </div>
  </div>
  <div class="slides-grid slides-grid--hero">
    <a class="slide-link slide-link--primary" href="#workshop-slides">Workshop Slides</a>
    <a class="slide-link slide-link--cheatsheet" href="{{ '/cheat-sheets/openstack/' | relative_url }}">Cheat Sheet</a>
  </div>
  <p>
    The lab runs on one bare-metal host with the cluster nodes as virtual
    machines. You work with them like physical servers, networking included,
    and the configuration is the one you would use on bare metal. The VMs you
    create in OpenStack run on top. The cloud uses Ceph as its storage
    backend.
  </p>
  <p>
    After the lab you understand how the pieces fit together and can
    deploy, administer and extend the cloud yourself. You also have what
    you need to find and fix the usual problems.
  </p>
  <ul>
    <li>Multi-node deployment with kolla-ansible</li>
    <li>Core services: Keystone, Nova, Neutron, Glance, Cinder, Horizon, Placement</li>
    <li>Networking with Open vSwitch: flat, VLAN, VXLAN, floating IPs, load balancing</li>
    <li>Day two: replacing nodes, live migration, upgrades, backup and recovery</li>
    <li>Monitoring and central logging with Prometheus, Grafana and OpenSearch</li>
    <li>Automation with cloud-init, Ansible and Terraform</li>
  </ul>
  <p>
    You need: one bare-metal server with nested virtualization enabled.
    The lab environment is allocated:
  </p>
  <ul>
    <li>🔲 60 vCPUs</li>
    <li>🗄️ 160 GB RAM</li>
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
    <img src="{{ '/assets/img/openstack/env.svg' | relative_url }}" alt="OpenStack workshop environment diagram"/>
  </div>
  <span class="section-sublabel">Automatic build and state</span>
  {% include build-info.html data=site.data.learn.openstack.build_info status=site.data.learn.openstack.build_status %}
  {% include status-panel.html data=site.data.learn.openstack.build_status %}
</div>

<div class="section">
  <h2 class="section-label">On real bare metal</h2>
  <p>
    The same configuration runs on real hardware. The following parts differ:
  </p>
  <ul>
    <li>Network interface names, bonding and VLAN trunks on the switch</li>
    <li>Disks and partitioning, including the devices used for Ceph OSDs</li>
    <li>BIOS and firmware settings, such as virtualization and boot mode</li>
    <li>Provisioning of the servers: PXE, IPMI or BMC access</li>
  </ul>
</div>

<div class="section" id="workshop-slides">
  <h2 class="section-label">Workshop slides</h2>
  <p><strong>OpenStack</strong></p>
  <div class="slides-grid">
    <a class="slide-link slide-link--primary" href="{{ '/learn/openstack/slides.html' | relative_url }}">View slides</a>
    <a class="slide-link" href="{{ '/learn/openstack/slides.pdf' | relative_url }}">Download PDF</a>
    <a class="slide-link" href="{{ '/learn/openstack/slides.pptx' | relative_url }}">Download PPTX</a>
    <a class="slide-link slide-link--cheatsheet" href="{{ '/cheat-sheets/openstack/' | relative_url }}">Cheat Sheet</a>
  </div>
  <p><strong>Testing</strong></p>
  <div class="slides-grid">
    <a class="slide-link slide-link--primary" href="{{ '/learn/openstack/slides-testing.html' | relative_url }}">View slides</a>
    <a class="slide-link" href="{{ '/learn/openstack/slides-testing.pdf' | relative_url }}">Download PDF</a>
    <a class="slide-link" href="{{ '/learn/openstack/slides-testing.pptx' | relative_url }}">Download PPTX</a>
  </div>
  <p><strong>Automation</strong></p>
  <div class="slides-grid">
    <a class="slide-link slide-link--primary" href="{{ '/learn/openstack/slides-automation.html' | relative_url }}">View slides</a>
    <a class="slide-link" href="{{ '/learn/openstack/slides-automation.pdf' | relative_url }}">Download PDF</a>
    <a class="slide-link" href="{{ '/learn/openstack/slides-automation.pptx' | relative_url }}">Download PPTX</a>
  </div>
  <div class="pill-row">
    <span class="pill">Free</span>
    <span class="pill">Advanced</span>
    <span class="pill">3 days</span>
    <span class="pill">English</span>
  </div>
  <div>
    <p>
      Want this delivered as a guided workshop? Write to
      <a href="mailto:ping@infraguide.org">ping@infraguide.org</a>.
    </p>
  </div>
</div>
