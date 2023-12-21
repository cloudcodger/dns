# Primary and Secondary DNS servers

Here is an example of how to use the `bind9_servers` role to configure a multiple DNS servers as a single primary and multiple secondary name servers. In this configuration a single host is the primary for all of the zones.

This assumes multiple `host`s exist in the ansible group `name_server` and are configured with an `ansible_user` other than `root`. If the `ansible_user=root` then `become: true` can be left out.

Configuring multiple DNS servers like this requires that the `bind9_servers_zones` _must_ be set using both `groups_vars` for the secondary name servers and `host_vars` for the primary name server. This is how you set different values for the name servers. The host `ns0` would be the primary name server in this example, with an IP address of `192.168.1.99`.

## Example Playbook calling `vars_files`

This example shows the `name_server_playbook.yml` that reads other files for the resource records and sets other variables using `group_vars`. See the `zone_vars.md` file for example file contents set under `vars_files:`.

```yaml
---
- name: Configure a Bind9 Name Server.
  become: true
  hosts: name_server
  vars_files:
    - 'zone_vars/192.168.6.yml'
    - 'zone_vars/lab.codger.site.yml'
  roles:
    - role: cloudcodger.dns.bind9_servers
```

`group_vars/name_server.yml` file (secondaries).

```yaml
---
bind9_servers_zones:
  - name: example.com
    primaries:
      - "192.168.1.99"
  - name: "1.168.192.in-addr.arpa"
    primaries:
      - "192.168.1.99"
```

`host_vars/ns0.yml` file (primary).

```yaml
---
bind9_servers_zones:
  - name: example.com
    records_var: zone_example_com
  - name: "1.168.192.in-addr.arpa"
    records_var: zone_192_168_1
```
