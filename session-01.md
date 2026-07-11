# Session 01: Lab Architecture & Local Workspace Initialization
**Date:** July 11, 2026  
**Role Focus:** Automated Infrastructure / DevOps Engineering

## 🎯 Objectives Completed
- [x] **Upstream Fixes:** Patched upstream 404 mirror errors by migrating to the verified `bento/rockylinux-9` base image ecosystem.
- [x] **Storage Stabilization:** Isolated workspace out of active OneDrive cloud sync boundaries into native paths (`C:\infra-homelab`) to prevent continuous virtual disk locks.
- [x] **Hypervisor Tuning:** Solved Windows 11 virtualization scheduling timeouts by injecting a `600`-second boot threshold (`config.vm.boot_timeout = 600`) into the configuration matrix.
- [x] **Connectivity Verification:** Successfully executed automated cluster initialization (`vagrant up`), established an interactive shell via host port forwards (`vagrant ssh control`), and validated host-only internal communication links.
- [x] **Graceful Power Down:** Validated target infrastructure lifecycle controls using ACPI shutdown commands (`vagrant halt`).

---

## 🏗️ Production-Ready `Vagrantfile` Architecture
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Mitigate Windows 11 scheduling delays by expanding the handshake window
  config.vm.boot_timeout = 600
  config.vm.box = "bento/rockylinux-9"

  # 1. Control Plane Node (Automation Engines & Private Registries)
  config.vm.define "control" do |control|
    control.vm.hostname = "control.lab"
    control.vm.network "private_network", ip: "192.168.56.10"
    control.vm.provider "virtualbox" do |vb|
      vb.name = "lab-control"
      vb.memory = "2048"
      vb.cpus = 2
      vb.customize ["modifyvm", :id, "--groups", "/AutomatedInfraLab"]
    end
  end

  # 2. Application Server Node (Container Deployments)
  config.vm.define "app" do |app|
    app.vm.hostname = "app-01.lab"
    app.vm.network "private_network", ip: "192.168.56.11"
    app.vm.provider "virtualbox" do |vb|
      vb.name = "lab-app-01"
      vb.memory = "1024"
      vb.cpus = 1
      vb.customize ["modifyvm", :id, "--groups", "/AutomatedInfraLab"]
    end
  end

  # 3. Database Server Node (Persistent Data Engines)
  config.vm.define "db" do |db|
    db.vm.hostname = "db-01.lab"
    db.vm.network "private_network", ip: "192.168.56.12"
    db.vm.provider "virtualbox" do |vb|
      vb.name = "lab-db-01"
      vb.memory = "2048"
      vb.cpus = 1
      vb.customize ["modifyvm", :id, "--groups", "/AutomatedInfraLab"]
    end
  end
end

```

---

## 🛠️ Operational Command Reference

* `vagrant up` -> Programmatically deploy and network the 3-node cluster.
* `vagrant status` -> Audit active hypervisor engine states.
* `vagrant ssh control` -> Drop into the secure automation control plane terminal.
* `vagrant halt` -> Cleanly flush VM disk states and perform a graceful ACPI shutdown.

---

## 📋 Next Session Backlog (Session 02)

* [ ] Initialize git repository components on your GitHub page profile.
* [ ] Map internal node aliases inside `/etc/hosts` across all 3 backend instances to handle local DNS resolution.
* [ ] Generate local SSH keypairs on `control.lab` and distribute them to prepare for configuration automation.

```

```
