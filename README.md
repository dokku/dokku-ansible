# dokku-ansible

A plugin for automatically running an ansible playbook against a dokku installation for a given app.

## Requirements

dokku 0.28.x+

## Installation

```shell
# on 0.19.x+
sudo dokku plugin:install https://github.com/dokku/dokku-ansible.git ansible
sudo dokku plugin:install-dependencies
```

## Usage

> Warning: This plugin allows full access to the ansible ecosystem, and thus should be used in a controlled environment as some actions can be dangerous in untrusted hands. 

Create a file named `.dokku/ansible/playbook.yml`. Example contents are shown below:

```yaml
---
- hosts: all
  vars:
    app: app-name

  roles:
    - role: dokku_bot.ansible_dokku

  tasks:
    - name: Setting the builder to the default value
      dokku_builder:
        app: "{{ app }}"
        property: selected
```

Specify a `.dokku/ansible/requirements.yml` file with your playbook requirements. For example, the following will include the official Dokku ansible modules:

```yaml
---

roles:
  - name: dokku_bot.ansible_dokku
```

Commit these changes and push to your dokku server. Changes will be applied prior to the build step.

```shell
git add .dokku
git commit -m "automate some dokku settings"
git push dokku master
```

### Errata

- The inventory file is automatically generated as `localhost`, and cannot be overriden.
- Ansible galaxy dependencies can be declared in `.dokku/ansible/requirements.yml`
- Tasks from the following roles will be automatically deleted to avoid any apt installation calls:
    - `dokku_bot.ansible_dokku`
    - `geerlingguy.docker`
    - `nginxinc.nginx`
- There is a special `app` variable that is automatically provided and can be used to reference the app being deployed.
- All commands are executed as the `dokku` user, and thus anything requiring root/sudo access will fail.
- The `ansible` and `ansible-galaxy` binaries _must_ be available on the default `$PATH`, otherwise this plugin will fail. Please see the installation section for more information.
