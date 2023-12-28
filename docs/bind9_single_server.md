# Single DNS server

Here is an example of how to use the `bind9_servers` role to configure a single standalone DNS server.

This assumes a `host` exists in the ansible group `name_server` (from `hosts: name_server`) and is configured with an `ansible_user` other than `root`. If `ansible_user=root` then `become: true` can be left out.

## Example Playbook

This example shows using role parameters for the all variables directly in `name_server_playbook.yml`.

```yaml
---
- name: Configure a Bind9 Name Server.
  become: true
  hosts: name_server
  roles:
    - role: cloudcodger.dns.bind9_servers
      bind9_servers_zones:
        - name: example.com
          records_var: zone_example_com
        - name: "1.168.192.in-addr.arpa"
          records_var: zone_192_168_1
      zone_example_com:
        - 'ctrl1;;A;192.168.1.21'
        - 'ctrl2;;A;192.168.1.22'
        - 'ctrl3;;A;192.168.1.23'
        - 'ns0;;A;192.168.1.99'
        - 'pve1;;A;192.168.1.251'
        - 'pve2;;A;192.168.1.252'
        - 'pve3;;A;192.168.1.253'
        - 'pxe;;A;192.168.1.9'
        - 'router;;A;192.168.1.1'
        - 'work1;;A;192.168.1.24'
        - 'work2;;A;192.168.1.25'
        - 'work3;;A;192.168.1.26'
        - 'zabbix;;A;192.168.1.8'
      zone_192_168_1:
        - '1;;PTR;router.example.com.'
        - '8;;PTR;zabbix.example.com.'
        - '9;;PTR;pxe.example.com.'
        - '21;;PTR;ctrl1.example.com.'
        - '22;;PTR;ctrl2.example.com.'
        - '23;;PTR;ctrl3.example.com.'
        - '24;;PTR;work1.example.com.'
        - '25;;PTR;work2.example.com.'
        - '26;;PTR;work3.example.com.'
        - '99;;PTR;ns0.example.com.'
        - '251;;PTR;pve1.example.com.'
        - '252;;PTR;pve2.example.com.'
        - '253;;PTR;pve3.example.com.'
```

## Example Playbook using `group_vars`

When the values for the variables, `bind9_servers_zones`, `zone_example_com` and `zone_192_168_1` are set in a `group_vars/name_server.yml` file, the playbook can just contain the following. They could also be set in `host_vars/ns0.yml` (assuming the host is `ns0`) instead.

```yaml
---
- name: Configure a Bind9 Name Server.
  become: true
  hosts: name_server
  roles:
    - role: cloudcodger.dns.bind9_servers
```

## Example Playbook using `vars_files`

This example shows the `name_server_playbook.yml` that reads other files for the resource records and sets other variables using `group_vars`. See the `zone_vars.md` file for example file contents set under `vars_files:`.

```yaml
---
- name: Configure a Bind9 Name Server.
  become: true
  hosts: name_server
  vars_files:
    - 'zone_vars/192.168.1.yml'
    - 'zone_vars/lab.codger.site.yml'
  roles:
    - role: cloudcodger.dns.bind9_servers
```

`group_vars/name_server.yml` file.

```yaml
---
bind9_servers_zones:
  - name: example.com
    records_var: zone_example_com
  - name: "1.168.192.in-addr.arpa"
    records_var: zone_192_168_1
```
