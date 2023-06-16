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

If you define multiple servers (master nodes) you should be providing a loadbalanced apiserver endpoint to all masters here. This default value is only suitable for a non-HA setup, if used in a HA setup, it will not protect you if the first node fails.

```yaml
k3s_api_server: "{{ hostvars[groups['server'][0]]['ansible_host'] | default(groups['server'][0]) }}"
```

To change API port use following variable:

```yaml
k3s_api_port: 6443
```

You should definitly change the default value for token so that servers can talk together securely.

```yaml
k3s_token: 'mysupersecuretoken'
```

In case you need to provide extra server or agent configuration, you can do so by editing `config.yaml.j2` template.

For more details on available server args refer to [docs](https://docs.k3s.io/cli/server). And for more details on available agent args refer to [docs](https://docs.k3s.io/cli/agent). Details on `config.yaml` can be found [here](https://docs.k3s.io/installation/configuration#configuration-file).

Dependencies
------------

None.

Example Playbook and Inventory
------------------------------

```yaml
- name: Configure K3s master nodes
  hosts: server
  roles:
    - role: ansible-role-k3s-pi

- name: Configure K3s agent nodes
  hosts: agent
  roles:
    - role: ansible-role-k3s-pi
```

```yaml
all:
  children:
    server:
      hosts:
        pi_m1:
        pi_m2:
        pi_m3:
    agent:
      hosts:
        pi_n1:
        pi_n2:
        pi_n3:
        pi_n4:
        pi_n5:
    k3s_cluster:
      server:
      agent:
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
