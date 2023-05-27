ansible-role-k3s-pi
=========================

This role configures K3s on Raspberry Pi OS.

Requirements
------------

None.

Role Variables
--------------

All default variables are set in `defaults/main.yml`. To change K3s version use following variable:

```yaml
k3s_version: v1.27.1+k3s1
```

To change K3s data directory, you can use following variable:

```yaml
k3s_data_dir: /var/lib/rancher/k3s
```

If you define multiple masters you should be providing a loadbalanced apiserver endpoint to all masters here. This default value is only suitable for a non-HA setup, if used in a HA setup, it will not protect you if the first node fails.

```yaml
k3s_api_server: "{{ hostvars[groups['master'][0]]['ansible_host'] | default(groups['master'][0]) }}"
```

You should definitly change the default value for token so that masters can talk together securely.

```yaml
k3s_token: "mysupersecuretoken"
```

For HA setup, you'll need to initialise the cluster first in order to join masters. You can tweak the args with following variable:

```yaml
server_init_args: >-
  {% if groups['master'] | length > 1 %}
    {% if ansible_host == hostvars[groups['master'][0]]['ansible_host'] | default(groups['master'][0]) %}
      --cluster-init
    {% else %}
      --server https://{{ hostvars[groups['master'][0]]['ansible_host'] | default(groups['master'][0]) }}:6443
    {% endif %}
    --token {{ k3s_token }}
  {% endif %}
  {{ extra_server_args | default('') }}
```

In case you need to provide extra server arguments, you can do so with following variable:

```yaml
k3s_extra_server_args: ""
```

For more details on available server args refer to [K3s docs](https://docs.k3s.io/cli/server).

In case you nee to provide extra agent arguments, you can do so with following variable:

```yaml
k3s_extra_agent_args: ""
```

For more details on available agent args refer to [K3s docs](https://docs.k3s.io/cli/agent).

Dependencies
------------

None.

Example Playbook and Inventory
------------------------------

```yaml
- name: Configure K3s masters
  hosts: master
  roles:
    - role: ansible-role-k3s-pi

- name: Configure K3s nodes
  hosts: node
  roles:
    - role: ansible-role-k3s-pi
```

```yaml
all:
  children:
    master:
      hosts:
        pi_m1:
        pi_m2:
        pi_m3:
    node:
      hosts:
        pi_n1:
        pi_n2:
        pi_n3:
        pi_n4:
        pi_n5:
    k3s_cluster:
      master:
      node:
```

Uninstalling K3s
---------------------

You can uninstall K3s by using same playbook and inventory with `unistall` tag:

```shell
ansible-playbook site.yml -i inventory.yml --tags uninstall
```

License
-------

MIT

Author Information
------------------

Created by [frennky](https://github.com/frennky).
