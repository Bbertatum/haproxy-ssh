# Ansible Role: haproxy-ssh

Installs HAProxy for Debian/Proxmox (maybe ubuntu) and create specific systemd service

Simple behaviour : Create reverse ssh proxy for Containers/VM

From 22{{containerID}} to container:22

## Requirements

Containers with LXC or vm running

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

The socket through which HAProxy can communicate (for admin purposes or statistics). To disable/remove this directive, set `haproxy_socket: ''` (an empty string).

    haproxy_socket: /var/lib/haproxy/stats

The jail directory where chroot() will be performed before dropping privileges. To disable/remove this directive, set `haproxy_chroot: ''` (an empty string). Only change this if you know what you're doing!

    haproxy_chroot: /var/lib/haproxy

The user and group under which HAProxy should run. Only change this if you know what you're doing!

    haproxy_user: haproxy
    haproxy_group: haproxy

Frontend configuration directives.

    frontend_name: "ssh{{ctid}}"
    frontend_bind_address: 'MYPUBLICIP'
    frontend_port: "22{{ctid}}"
    frontend_mode: 'tcp'
    
Backend settings.

    backend_name: "ssh_{{ctid}}"
    backend_mode: 'tcp'
    backend_server: "ssh{{ctid}}"
    backend_address: "192.168.1.{{ctid}}:22"

    haproxy_global_vars:
      - 'ssl-default-bind-ciphers ABCD+KLMJ:...'
      - 'ssl-default-bind-options no-sslv3'

## Dependencies

systemd 

## Example Playbook

    ---
    - hosts: all
    
      remote_user: root
      vars_prompt:
        name: "ctid"
        prompt: "CTID ? "
        private: false
      vars:
    
        # Frontend settings.
        frontend_name: "ssh{{ctid}}"
        frontend_bind_address: 'PUBLICIP'
        frontend_port: "22{{ctid}}"
        frontend_mode: 'tcp'
    
        # Backend settings.
        backend_name: "ssh_{{ctid}}"
        backend_mode: 'tcp'
        backend_server: "ssh{{ctid}}"
        backend_address: "192.168.1.{{ctid}}:22"
    
        # Extra global vars (see README for example usage).
        haproxy_global_vars:
    
      roles:
      - name: haproxy-ssh
    
## License

MIT / BSD

## Author Information

This role was created in 2019 by [Bertrand Cebador](https://github.com/Bbertatum) inspired by the one originaly done by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
