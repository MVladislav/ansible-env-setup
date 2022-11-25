# Ansible Env Setup

```sh
    MVladislav
```

---

- [Ansible Env Setup](#ansible-env-setup)
  - [clone](#clone)
  - [install dependencies on host](#install-dependencies-on-host)
  - [setup](#setup)
    - [device](#device)
    - [ssh-key](#ssh-key)
  - [run](#run)
    - [setup a client](#setup-a-client)
    - [setup a server](#setup-a-server)

---

## clone

clone this repo recursive, roles are included as submoduls

```sh
$git clone --recursive https://github.com/MVladislav/ansible-env-setup.git
```

to load or update submodules afterwards run:

```sh
: 'load'
$git submodule update --init --recursive
: 'update'
$git submodule update --recursive --remote
```

## install dependencies on host

install Ansible on host to run the playbook

```sh
$sudo apt install python3 python3-pip sshpass
: 'optional: openssh-sftp-server'
$python3 -m pip install Ansible
$python3 -m pip install molecule[docker] ansible-lint
```

## setup

### device

copy `inventory/inventory-example.yml` to `inventory/inventory.yml`:

```sh
$cp inventory/inventory-example.yml inventory/inventory.yml
```

add the device information into: `inventory/inventory.yml`

main playbooks (includes multiple sub-playbooks):

- `playbooks/playbook-client.yml`
- `playbooks/playbook-client-vm.yml`
- `playbooks/playbook-client-dev.yml`
- `playbooks/playbook-server.yml`
- `playbooks/playbook-server-minimal.yml`
- `playbooks/playbook-server-cluster.yml`

if needed you can update this, \
like you need to install services.

### ssh-key

copy `playbooks/vars/default-example.yml` to `playbooks/vars/default.yml`:

```sh
$cp playbooks/vars/default-example.yml playbooks/vars/default.yml
```

every setup will add a **ssh-pub-key** to the **nodes**,\
for that you need to add a **ssh-pub-key** into `playbooks/vars/default.yml`\
where the **key**, in the file, will be selected by: \
`"{{ ansible_user }}-{{ ansible_host }}"`

this process is done in: `playbooks/pre-tasks.yml`

## run

### setup a client

> HINT: playbooks are pre-defined to install multiple different service. \
> Change them as you need

on first run:

> `-k` => will use **ssh with a password**, as a fresh setup has no **ssh-key**

```sh
$ansible-playbook playbooks/playbook-client.yml --ask-become-pass -k
```

on other runs (because ssh is/should be configured with ssh-key):

```sh
$ansible-playbook playbooks/playbook-client.yml --ask-become-pass
```

### setup a server

> HINT: playbooks are pre-defined to install multiple different service. \
> Change them as you need

on first run:

> `-k` => will use **ssh with a password**, as a fresh setup has no **ssh-key**

```sh
$ansible-playbook playbooks/playbook-server.yml --ask-become-pass -k
```

on other runs:

```sh
$ansible-playbook playbooks/playbook-server.yml --ask-become-pass
```
