# teleport

Role for deploy Teleport - the easiest, and most secure way to access and
protect all of your infrastructure

## Requirements

* Ansible 9.0.0+;

## Example configuration

```yaml
---
teleport:
# Enable teleport service or not
  - enable: 'true'
# Restart teleport service after deploy or not
    restart: 'true'
# Install teleport package or not
    install_package: 'true'
# 'present' (do nothing if package is already installed) or 'latest' (always
# upgrade to last version)
    package_state: 'latest'
    settings:
      # Configuration file version. The current version is 'v3'
      version: 'v3'
      auth_service:
        enabled: false
      proxy_service:
        enabled: false
      teleport:
        nodename: "{{ inventory_hostname }}"
        data_dir: '/var/lib/teleport'
        log:
          output: 'stdout'
          severity: 'WARN'
          format:
            output: 'text'
        diag_addr: "{{ ansible_host + ':2222' }}"
        auth_token: 'my_teleport_secret'
        proxy_server: "{{ (lookup('ansible.builtin.vars',
          'bastion').split('.')[0] | ansible.builtin.mandatory('The Bastion
          hostname is required')) + ':3080' }}"
      ssh_service:
        enabled: true
        disable_create_host_user: true
        listen_addr: "{{ ansible_host + ':2443' }}"
        commands:
          - name: 'fqdn'
            command:
              - 'hostnamectl'
              - 'status'
              - '--static'
            period: "1h0m0s"
        labels:
          project: "{{ lookup('ansible.builtin.vars',
            'netbox_device_project') }}"
          role: "{{ lookup('ansible.builtin.vars', 'netbox_device_role') }}"
          environment: "{{ lookup('ansible.builtin.vars',
            'netbox_device_environment') }}"
          region: "{{ lookup('ansible.builtin.vars', 'netbox_device_region') }}"
          zone: "{{ lookup('ansible.builtin.vars', 'netbox_device_zone') }}"
```
