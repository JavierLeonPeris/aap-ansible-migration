# Ansible Migration Guide: From 2.9 to Modern Ansible

This repository provides comprehensive guidance on migrating from Ansible 2.9 to modern Ansible versions (2.10+), with a focus on the major architectural change: the move from monolithic core to collections.

## Table of Contents

- [What Changed](#what-changed)
- [Key Migration Concepts](#key-migration-concepts)
- [Commonly Used Modules That Moved](#commonly-used-modules-that-moved)
- [Files in This Repository](#files-in-this-repository)
- [How to Use This Guide](#how-to-use-this-guide)
- [Understanding requirements.yml](#understanding-requirementsyml)
- [Official Documentation](#official-documentation)

## What Changed

### The Big Picture

Starting with Ansible 2.10, the project underwent a major restructuring:

- **Ansible 2.9 and earlier**: Most modules were bundled in `ansible-core` (monolithic package)
- **Ansible 2.10+**: Modules were moved to **Collections** - independently versioned packages maintained by community teams

### Why This Matters

1. **Fully Qualified Collection Names (FQCN)** are now required or strongly recommended
2. **Collections must be installed separately** using `ansible-galaxy`
3. **Module names include the collection namespace**: `community.general.mail` instead of just `mail`
4. **Different update cycles**: Collections can be updated independently from Ansible core

## Key Migration Concepts

### Fully Qualified Collection Names (FQCN)

**Old way (Ansible 2.9):**
```yaml
- name: Send email
  mail:
    to: user@example.com
```

**New way (Ansible 2.10+):**
```yaml
- name: Send email
  community.general.mail:
    to: user@example.com
```

The FQCN format is: `<namespace>.<collection>.<module_name>`

### Installing Collections

Collections are not included by default. 

In Ansible Automation Platform they will be installed while syncing the projects using the requirements.yml, use the examples from this guide.

Install them locally using:

```bash
# Install a single collection
ansible-galaxy collection install community.general

# Install from requirements file (recommended)
ansible-galaxy collection install -r requirements.yml

# Install to a specific path
ansible-galaxy collection install -p ./collections -r requirements.yml
```

## Commonly Used Modules That Moved

Here are the most frequently used modules and their new collection homes:

### Communication & Notifications

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `mail` | `community.general.mail` | community.general |
| `slack` | `community.general.slack` | community.general |
| `irc` | `community.general.irc` | community.general |

### Docker & Containers

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `docker_container` | `community.docker.docker_container` | community.docker |
| `docker_image` | `community.docker.docker_image` | community.docker |
| `docker_network` | `community.docker.docker_network` | community.docker |
| `docker_compose` | `community.docker.docker_compose` | community.docker |

### Database Management

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `mysql_db` | `community.mysql.mysql_db` | community.mysql |
| `mysql_user` | `community.mysql.mysql_user` | community.mysql |
| `postgresql_db` | `community.postgresql.postgresql_db` | community.postgresql |
| `postgresql_user` | `community.postgresql.postgresql_user` | community.postgresql |
| `mongodb_user` | `community.mongodb.mongodb_user` | community.mongodb |

### Cloud Providers - AWS

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `ec2_instance` | `amazon.aws.ec2_instance` | amazon.aws |
| `ec2_instance_facts` | `amazon.aws.ec2_instance_info` | amazon.aws |
| `s3_bucket` | `amazon.aws.s3_bucket` | amazon.aws |
| `rds` | `amazon.aws.rds_instance` | amazon.aws |
| `cloudformation` | `amazon.aws.cloudformation` | amazon.aws |

### Cloud Providers - Azure

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `azure_rm_virtualmachine` | `azure.azcollection.azure_rm_virtualmachine` | azure.azcollection |
| `azure_rm_storageaccount` | `azure.azcollection.azure_rm_storageaccount` | azure.azcollection |

### Network Devices

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `ios_config` | `cisco.ios.ios_config` | cisco.ios |
| `ios_command` | `cisco.ios.ios_command` | cisco.ios |
| `nxos_config` | `cisco.nxos.nxos_config` | cisco.nxos |
| `junos_config` | `junipernetworks.junos.junos_config` | junipernetworks.junos |
| `eos_config` | `arista.eos.eos_config` | arista.eos |

### System Utilities

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `synchronize` | `ansible.posix.synchronize` | ansible.posix |
| `mount` | `ansible.posix.mount` | ansible.posix |
| `selinux` | `ansible.posix.selinux` | ansible.posix |
| `firewalld` | `ansible.posix.firewalld` | ansible.posix |

### Monitoring & Observability

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `zabbix_host` | `community.zabbix.zabbix_host` | community.zabbix |
| `datadog_monitor` | `community.general.datadog_monitor` | community.general |
| `nagios` | `community.general.nagios` | community.general |

### Package Management

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `npm` | `community.general.npm` | community.general |
| `pip` | `ansible.builtin.pip` | ansible.builtin (still in core) |
| `gem` | `community.general.gem` | community.general |

### Windows Management

| Ansible 2.9 Module | New Collection FQCN | Collection Name |
|-------------------|---------------------|-----------------|
| `win_certificate_store` | `community.windows.win_certificate_store` | community.windows |
| `win_disk_facts` | `community.windows.win_disk_facts` | community.windows |
| `win_firewall_rule` | `community.windows.win_firewall_rule` | community.windows |

## Files in This Repository

### [playbook_old.yml](playbook_old.yml)
Example playbook using modules as they were in Ansible 2.9 - using short module names without collection namespaces. This demonstrates the "old way" of writing playbooks.

### [playbook_new.yml](playbook_new.yml)
The same playbook updated for modern Ansible (2.10+) using Fully Qualified Collection Names (FQCN). This demonstrates best practices for new playbooks.

**Key Differences:**
- Uses FQCN format: `community.general.mail` instead of `mail`
- Requires collections to be installed via `requirements.yml`
- More explicit and maintainable

### [ansible_builtin_runtime.yml](ansible_builtin_runtime.yml)
Complete reference file from the official Ansible repository containing the routing information for ALL modules that have been moved from ansible-core to collections. This file shows:
- Original module names
- New collection locations (redirects)
- Deprecated modules
- Removed modules (tombstones)

**Note:** This is the authoritative source for migration mappings directly from the Ansible project.

### [requirements.yml](requirements.yml)
Example collections requirements file showing how to declare and install collection dependencies for your Ansible projects.

## How to Use This Guide

### 1. Identify Modules You're Using

Review your existing Ansible 2.9 playbooks and identify which modules you're using. Cross-reference them with the tables above or search in [ansible_builtin_runtime.yml](ansible_builtin_runtime.yml).

### 2. Create a requirements.yml File

Based on the modules you need, create a `requirements.yml` file listing all required collections. See the example [requirements.yml](requirements.yml) in this repository.

### 3. Install Collections

```bash
ansible-galaxy collection install -r requirements.yml
```

### 4. Update Your Playbooks

Convert module names to use FQCN. Compare [playbook_old.yml](playbook_old.yml) and [playbook_new.yml](playbook_new.yml) to see the conversion pattern.

### 5. Test Thoroughly

Run your updated playbooks in a test environment to ensure all modules work as expected.

## Understanding requirements.yml

The `requirements.yml` file is used to manage Ansible collection dependencies, similar to how `package.json` works for Node.js or `requirements.txt` for Python.

### Basic Structure

```yaml
---
collections:
  - name: community.general
    version: ">=8.0.0"
  
  - name: community.docker
    version: ">=3.4.0"
```

### Key Fields

- **name**: The collection namespace and name (format: `namespace.collection`)
- **version**: Version constraint (supports `>=`, `==`, `<=`, version ranges)
- **source**: (Optional) Custom Galaxy server or Git repository URL

### Installing Collections

If you are running the playbooks in Ansible Automation Platform, this will be done automatically. Ignore this section.

```bash
# Install from requirements.yml
ansible-galaxy collection install -r requirements.yml

# Force reinstall (useful for updates)
ansible-galaxy collection install -r requirements.yml --force

# Install to custom directory
ansible-galaxy collection install -r requirements.yml -p ./collections

# Upgrade collections to latest compatible versions
ansible-galaxy collection install -r requirements.yml --upgrade
```

### Where Collections Are Installed

By default, collections are installed to:
- `~/.ansible/collections/ansible_collections/` (user-level)
- `/usr/share/ansible/collections/ansible_collections/` (system-level)
- `./collections/ansible_collections/` (project-level, if using `-p ./collections`)

### Best Practices

1. **Version pinning**: Use version constraints to ensure compatibility
2. **Document dependencies**: Comment each collection explaining what it's used for
3. **Regular updates**: Keep collections updated for bug fixes and new features
4. **Project-local installations**: Consider using `-p ./collections` for project-specific versions

## Official Documentation

### Migration Guides

- **Official Ansible Porting Guide**: https://docs.ansible.com/ansible/latest/porting_guides/porting_guides.html
- **Ansible 2.10 Porting Guide**: https://docs.ansible.com/ansible/latest/porting_guides/porting_guide_2.10.html
- **Migration Guide Index**: https://docs.ansible.com/ansible/latest/migration_guide/index.html

### Collections Documentation

- **Using Collections**: https://docs.ansible.com/ansible/latest/user_guide/collections_using.html
- **ansible-galaxy CLI**: https://docs.ansible.com/ansible/latest/cli/ansible-galaxy.html
- **Collection Index**: https://docs.ansible.com/ansible/latest/collections/index.html

### Specific Collection Documentation

- **community.general**: https://docs.ansible.com/ansible/latest/collections/community/general/
- **community.docker**: https://docs.ansible.com/ansible/latest/collections/community/docker/
- **amazon.aws**: https://docs.ansible.com/ansible/latest/collections/amazon/aws/
- **cisco.ios**: https://docs.ansible.com/ansible/latest/collections/cisco/ios/

### Additional Resources

- **Ansible Galaxy** (browse/search collections): https://galaxy.ansible.com/
- **Ansible Community Guide**: https://docs.ansible.com/ansible/latest/community/index.html
- **Release Notes**: https://github.com/ansible/ansible/blob/devel/changelogs/

## Quick Reference

### Check Installed Collections

```bash
ansible-galaxy collection list
```

### Verify Ansible Version

```bash
ansible --version
```

### Search for Collections

```bash
ansible-galaxy collection search <keyword>
```

### View Collection Documentation

```bash
ansible-doc community.general.mail
```

## Contributing

Found an issue or want to add more examples? Feel free to contribute to this guide!

