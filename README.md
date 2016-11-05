Consul
=========

This role currently installs and configures consul as a docker container on the hosts.
The consul container uses the [official image](https://hub.docker.com/_/consul/) and supports consul v0.7.0 upwards.

See the tests directory how to setup consulservers and agents.

Role Variables
--------------
Here is a list of all the default variables for this role, which are also available in `defaults/main.yml`.

```yml
---
# defining consul version and image to use
consul_version: '0.7.0'
consul_image: "consul"
consul_image_and_tag: "consul:{{consul_version}}"
consul_home: /opt/consul
consul_dir_config: "{{consul_home}}/config"
consul_dir_data: "{{consul_home}}/data"
consul_volumes:
  - "{{consul_dir_data}}:/consul/data"
  - "{{consul_dir_config}}:/consul/config"

consul_domain: consul
# list of the consul servers
consul_datacenter: "dc1"

consul_is_ui: false
consul_is_server: false

consul_disable_update_check: true

consul_servers: ['127.0.0.1']
consul_bind_addr: "0.0.0.0"
consul_dynamic_bind: false
consul_client_address: "{{consul_servers | intersect(ansible_all_ipv4_addresses) | first | default('127.0.0.1')}}"
consul_client_address_bind: false

consul_leave_on_terminate: true
consul_rejoin_after_leave: true
consul_acl_datacenter: "dc1"
consul_advertise_address: "{{consul_servers | intersect(ansible_all_ipv4_addresses) | first | default('127.0.0.1')}}"
consul_bootstrap_expect: "{{consul_servers | length }}"
consul_dns_config: false
consul_dns_allow_stale: false
consul_dns_max_stale: 5s
consul_dns_node_ttl: 0s
consul_dns_service_ttl: 0s
consul_dns_enable_truncate: false
consul_dns_only_passing: false
consul_recursors: []

consul_join_at_start: false
consul_retry_join: false
consul_retry_interval: 30s
consul_retry_max: 0

consul_servers_wan: []
consul_join_wan: false
consul_retry_join_wan: false
consul_retry_interval_wan: 30s
consul_retry_max_wan: 0
consul_advertise_address_wan: false

consul_log_level: "INFO"

# ACL support, here not enforced
#consul_acl_datacenter: 'dc1'
#consul_acl_default_policy:
#consul_acl_down_policy:
#consul_acl_master_token:
#consul_acl_token:
#consul_acl_ttl:
#consul_atlas_acl_token:

# Telemetry can be set
# consul_statsd_address:
# consul_statsite_address:
# consul_statsite_prefix:
# consul_cors_support:

# consul_skip_leave_on_interrupt

consul_port_dns: 8600
consul_port_http: 8500
consul_port_https: -1
consul_port_rpc: 8400
consul_port_serf_lan: 8301
consul_port_serf_wan: 8302
consul_port_server: 8300

consul_node_name: "{{ inventory_hostname }}"
```

You could setup your consulhosts by defining them in the inventory:

```ini
[consulservers]
server1
server2
server3

[consulagents]
agent

[consulhosts:children]
consulservers
consulagents
```

and defining group variables for consulservers

```ini
consul_is_server: true
consul_is_ui: true
consul_join_at_start: true
consul_retry_join: true
consul_client_address_bind: true
```
and for consul client agents:

```ini
consul_join_at_start: true
consul_retry_join: true
consul_client_address_bind: true
```

Dependencies
------------

Docker must be installed on the consul hosts

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:
```yaml
---
- hosts: consulhosts
  roles:
  - role: rattermeyer.docker
    docker_group_members: vagrant
  - role: ansible-role-consul
    consul_servers:
    - "192.168.56.11"
    - "192.168.56.12"
    - "192.168.56.13"
    tags: consul
```

License
-------

BSD
