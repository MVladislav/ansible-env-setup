# Ansible Env Setup

[![Ansible Lint](https://github.com/MVladislav/ansible-env-setup/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/MVladislav/ansible-env-setup/actions/workflows/ansible-lint.yml)

- [Ansible Env Setup](#ansible-env-setup)
  - [Clone project](#clone-project)
  - [Install dependencies on host](#install-dependencies-on-host)
  - [Setup host](#setup-host)
  - [Setup targets](#setup-targets)
  - [Playbooks overview](#playbooks-overview)
    - [ansible-install-server](#ansible-install-server)
    - [ansible-install-client](#ansible-install-client)

---

## Clone project

Clone this repo recursive, roles are included as submoduls:

```sh
$git clone --recursive https://github.com/MVladislav/ansible-env-setup.git

# load
$git submodule update --init --recursive
# update
$git submodule update --recursive --remote
```

## Install dependencies on host

Install ansible on host to run the playbook:

```sh
# optional: openssh-sftp-server
$sudo apt install python3 python3-pip sshpass
$python3 -m pip install [--break-system-packages] ansible molecule molecule-plugins[docker] yamllint ansible-lint

# for use of "ansible-galaxy collection install"
$python3 -m pip install [--break-system-packages] -Iv "resolvelib<0.8.1"
```

Update ansible on host:

```sh
$python3 -m pip install [--break-system-packages] --upgrade ansible molecule molecule-plugins[docker] yamllint ansible-lint
$ansible-galaxy collection install --upgrade -r requirements.yml
```

## Setup host

Copy the inventory template `inventory/inventory-example.yml` as `inventory/inventory.yml`:

```sh
$cp inventory/inventory-example.yml inventory/inventory.yml
```

Copy **vars default** `playbooks/vars/default-example.yml` as `playbooks/vars/default.yml`
which holds the **ssh-keys** for setup in `pre-tasks.yml` defined by clients in `inventory.yml`:

> clients are identified in `playbooks/vars/default.yml` by **key** with `"{{ ansible_user }}-{{ ansible_host }}"`
> inside `inventory.yml` you can define in **`ansible_ssh_private_key_file`** the related **ssh-key**

```sh
$cp playbooks/vars/default-example.yml playbooks/vars/default.yml
```

Update `inventory/inventory.yml` with your own configuration as you need.
By default multiple playbooks are pre defined. Use and update them as you need.

Following are some main playbooks to install clients or servers:

- base
  - playbook-sec-short.yml
- clients
  - playbook-client.yml
  - playbook-client-vm.yml
  - playbook-client-dev.yml
  - playbook-client-pentest.yml
- servers
  - playbook-server-minimal.yml
  - playbook-server-dev.yml
  - playbook-server-cluster.yml

## Setup targets

> `-k` => will use **ssh with a password**, as a fresh setup has no **ssh-key** installed
> if **ssh-key** is installed on target you not need to inclide `-k`

```sh
$ansible-playbook playbooks/playbook-sec-short.yml --ask-become-pass -k
```

## Playbooks overview

In general following playbooks/roles/tasks are run by each client playbook with its specific configuration:

- playbook-s-cis
  > Harden the client by CIS rules
  - ansible-cis-ubuntu-2204
  - cis aide env extend
- playbook-s-pre-install
  > some pre installs and configs
  - pre-tasks
  - ansible-updater
  - ansible-ssh
  - ansible-netplan
  - community.general.ufw
  - ansible-postfix
  - nullmailer
- playbook-s-hardening
  > some more general client hardenings
  - ~~lynis~~
  - ansible-security
- [ansible-install-server](#ansible-install-server)
  > install tools/service for server usage, which also useful for clients
- [ansible-install-client](#ansible-install-client)
  > install tools/service for client usage
- ansible-docker
  > install docker with CIS harden

### ansible-install-server

- servers:
  - s1: default (TODO)
  - s2: minimal
  - s3: dev
  - s4: cluster
- clients:
  - c1: default
  - c2: vm
  - c3: dev
  - c4: pentest

| apps                    | s1  | s2  | s3  | s4  | c1  | c2  | c3  | c4  |
| :---------------------- | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| apt_base                |     |  x  |  x  |  x  |  x  |  x  |  x  |  x  |
| apt_auth_priv           |     |     |  x  |     |  x  |  x  |  x  |  x  |
| apt_cert                |     |  x  |  x  |  x  |  x  |  x  |  x  |  x  |
| apt_archive             |     |     |  x  |     |     |  x  |  x  |  x  |
| apt_dev                 |     |     |  x  |     |     |  x  |  x  |  x  |
| apt_build               |     |     |  x  |     |     |     |     |  x  |
| apt_libs                |     |     |  x  |     |     |     |     |  x  |
| apt_php                 |     |     |     |     |     |     |     |     |
| apt_lua                 |     |     |     |     |     |     |     |     |
| apt_java_jre_headless   |     |     |  x  |     |     |     |     |     |
| apt_java_jdk            |     |     |     |     |     |     |     |     |
| apt_java_ant            |     |     |     |     |     |     |     |     |
| apt_java_maven          |     |     |     |     |     |     |     |     |
| apt_java_gradle         |     |     |     |     |     |     |     |     |
| apt_snap                |     |     |  x  |     |  x  |  x  |  x  |  x  |
| apt_qemu_guest_agent    |     |  x  |  x  |  x  |     |  x  |     |  x  |
| apt_rasp_pi_pkg         |     |     |     |     |     |     |     |     |
| apt_vpn_resolvconf      |     |     |     |     |     |     |     |     |
| apt_vpn_wireguard       |     |     |     |     |     |     |     |     |
| apt_vpn_openvpn         |     |     |     |     |     |     |     |     |
| apt_vpn_openconnect     |     |     |     |     |     |     |     |     |
| apt_latex               |     |     |     |     |     |  x  |  x  |  x  |
| apt_pandoc              |     |     |     |     |     |  x  |  x  |  x  |
| apt_apt_john_the_ripper |     |     |     |     |     |     |     |  x  |
| apt_nmap                |     |     |     |     |     |  x  |  x  |  x  |
| snap_john_the_ripper    |     |     |     |     |     |     |     |     |
| snap_nmap               |     |     |  x  |     |     |     |     |     |
| snap_juju               |     |     |     |     |     |     |     |     |
| snap_maas               |     |     |     |     |     |     |     |     |
| snap_microk8s           |     |     |     |     |     |     |     |     |
| snap_kubectl            |     |     |     |     |     |     |     |     |
| snap_helm               |     |     |     |     |     |     |     |     |
| snap_multipass          |     |     |     |     |     |     |     |     |
| snap_btop               |     |     |  x  |     |  x  |  x  |  x  |  x  |
| snap_glow               |     |     |  x  |     |     |     |     |  x  |
| snap_go                 |     |     |     |     |     |     |     |     |
| snap_node               |     |     |     |     |     |     |     |     |
| snap_ruby               |     |     |     |     |     |     |     |     |
| snap_rust               |     |     |     |     |     |     |     |     |
| snap_openjdk            |     |     |     |     |     |     |     |     |
| snap_openjfx            |     |     |     |     |     |     |     |     |
| inst_git_conf           |     |  x  |  x  |  x  |  x  |  x  |  x  |  x  |
| inst_zsh_conf           |     |  x  |  x  |  x  |     |  x  |  x  |  x  |
| inst_tmux_conf          |     |  x  |  x  |     |     |  x  |  x  |  x  |
| inst_nvim_conf          |     |  x  |  x  |     |     |  x  |  x  |  x  |
| apt_python              |     |  x  |  x  |     |     |  x  |  x  |  x  |
| apt_python_pip          |     |  x  |  x  |     |     |  x  |  x  |  x  |
| apt_python_venv         |     |  x  |  x  |     |     |  x  |  x  |  x  |
| apt_python_dev          |     |     |  x  |     |     |  x  |  x  |  x  |
| pip_s_tui               |     |  x  |  x  |     |     |  x  |  x  |  x  |
| pip_virtualenv          |     |     |     |     |     |     |     |     |
| pip_autopep8            |     |     |  x  |     |     |  x  |  x  |  x  |
| pip_black               |     |     |  x  |     |     |  x  |  x  |  x  |
| pip_mypy                |     |     |  x  |     |     |  x  |  x  |  x  |
| pip_pre_commit          |     |     |  x  |     |     |  x  |  x  |  x  |
| pip_openconnect_sso     |     |     |     |     |     |     |     |     |
| pip_ansible             |     |     |  x  |     |     |  x  |  x  |     |
| go_kompose              |     |     |     |     |     |     |     |     |
| go_act                  |     |     |     |     |     |     |     |     |

### ansible-install-client

| services/tools           | default | dev | pentest | vm  |
| :----------------------- | :-----: | :-: | :-----: | :-: |
| dev                      |    x    |  x  |    x    |  x  |
| fonts                    |    x    |  x  |    x    |  x  |
| gnome additional's       |    x    |  x  |    x    |  x  |
| gnome dep.               |         |     |         |     |
| gnome ext.               |    x    |  x  |    x    |  x  |
| gnome ext. ubuntu tiling |         |     |         |     |
| gnome ext. caffeine      |         |     |         |     |
| gnome ext. sound         |         |     |         |     |
| gnome ext. blur shell    |    x    |  x  |    x    |  x  |
| gnome ext. burn window   |         |     |         |     |
| gnome ext. dash to panel |    x    |  x  |    x    |  x  |
| gnome ext. ui tune       |         |     |         |     |
| gnome keybinding         |         |     |         |     |
| gnome overlay            |    x    |  x  |    x    |  x  |
| gnome terminal overlay   |    x    |  x  |    x    |  x  |

| apps                     | default | vm  | dev | pentest |
| :----------------------- | :-----: | :-: | :-: | :-----: |
| base                     |    x    |  x  |  x  |    x    |
| auth_priv                |    x    |  x  |  x  |    x    |
| ubuntu                   |    x    |  x  |  x  |    x    |
| archive                  |         |  x  |  x  |    x    |
| codec                    |         |     |     |         |
| gnome                    |         |     |     |         |
| snap                     |    x    |  x  |  x  |    x    |
| flatpak                  |    x    |  x  |  x  |    x    |
| texmaker                 |         |     |     |         |
| logitech_unifying_solaar |         |     |     |         |
| mpv                      |         |     |     |         |
| vpn_resolvconf           |         |     |     |         |
| vpn_l2tp                 |         |     |     |         |
| vpn_openvpn              |         |     |     |         |
| vpn_openconnect          |         |     |     |         |
| vpn_wireguard            |         |     |     |         |
| gnome_boxes              |         |     |     |         |
| virt_viewer              |         |     |  x  |         |
| veracrypt                |    x    |  x  |  x  |    x    |
| veracrypt_cli            |         |     |     |         |
| virtualbox               |         |     |     |         |
| 1password_cli            |         |     |     |         |
| portmaster               |         |     |     |         |
| parsec                   |         |     |     |         |
| brim                     |         |     |     |    x    |
| logseq                   |         |  x  |  x  |    x    |
| ultimaker                |         |     |  x  |         |
|                          |         |     |     |         |
| 1password                |         |     |     |         |
| keepassxc                |         |     |     |         |
| yubioath                 |         |     |     |         |
| chromium                 |         |  x  |  x  |    x    |
| denaro                   |         |     |     |         |
| firefox                  |    x    |  x  |  x  |    x    |
| flameshot                |         |     |     |         |
| foliate                  |         |     |     |         |
| libreoffice              |         |     |     |         |
| newsflash                |         |     |     |         |
| okular                   |         |     |     |         |
| onlyoffice               |    x    |  x  |  x  |    x    |
| thunderbird              |    x    |     |  x  |         |
| xournalpp                |         |     |     |         |
| zoom                     |         |     |     |         |
| discord                  |         |     |     |         |
| jdownloader              |         |     |     |         |
| signal                   |    x    |     |  x  |         |
| telegram                 |    x    |     |  x  |         |
| blender                  |         |     |     |         |
| darktable                |         |     |  x  |         |
| drawio                   |    x    |     |  x  |    x    |
| gimp                     |    x    |     |  x  |         |
| inkscape                 |    x    |     |  x  |    x    |
| krita                    |         |     |     |         |
| lunacy                   |         |     |  x  |         |
| upscayl                  |         |     |  x  |    x    |
| amberol                  |         |     |     |         |
| haruna                   |    x    |  x  |  x  |    x    |
| obs                      |         |     |     |         |
| parabolic                |         |     |     |         |
| video_trimmer            |         |     |     |         |
| vlc                      |         |     |     |         |
| moosync                  |         |     |     |         |
| spotify                  |    x    |     |  x  |         |
| steam                    |         |     |     |         |
| android_studio           |         |     |     |    x    |
| beekeeper_studio         |         |     |     |    x    |
| code                     |         |  x  |  x  |    x    |
| dbeaver                  |         |     |     |    x    |
| insomnia                 |         |     |     |    x    |
| postman                  |         |     |     |         |
| remmina                  |         |  x  |  x  |    x    |
| rpi_imager               |         |     |     |         |
| ghidra                   |         |     |     |    x    |
| zaproxy                  |         |     |     |    x    |
|                          |         |     |     |         |
| mqtt_explorer            |         |     |     |    x    |
| UBports                  |         |     |     |         |
| fbreader                 |         |     |     |         |
| pixelfx                  |         |     |     |         |
|                          |         |     |     |         |
| cryptomator              |         |     |     |    x    |
| flatseal                 |    x    |  x  |  x  |    x    |
| ausweisapp2              |         |     |     |         |
| easy_effects             |         |     |  x  |         |
| extension_manager        |         |     |     |         |
| filezilla                |         |     |     |         |
| missioncenter            |         |     |     |         |
| planify                  |         |     |     |         |
| warp                     |         |     |     |    x    |
| threemaqt                |         |     |     |         |
| conjure                  |         |     |     |         |
| peek                     |         |     |     |         |
| girens                   |         |     |     |         |
| lutris                   |         |     |     |         |
| arduinoide               |         |     |     |         |
| betaflightconfigurator   |         |     |     |         |
| fritzing                 |         |     |     |         |
| mongodb_compass          |         |     |     |         |
| sublimetext              |         |     |     |         |
| wireshark                |         |     |     |         |
