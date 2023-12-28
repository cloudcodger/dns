# The standard name servers configuration with Primary and Secondary DNS servers

Here is an example of how to use the `bind9_servers` role to configure multiple DNS servers with a single primary and multiple secondary name servers for all of the configured zones. See `bind9_ns.md` for additional information.

The `bind9_servers` role supports having different primary name servers for each configured zone, however this configuration will likely be the most common (or standard) choice when configuring more than one name server.

This assumes multiple `host`s exist in the ansible group `name_server` and are configured with an `ansible_user` other than `root`. If `ansible_user=root` then `become: true` can be left out.

Configuring multiple DNS servers like this requires that the `bind9_servers_zones` _must_ be set in two places. Once under the `groups_vars` with settings for the secondary name servers. And once under the `host_vars` with settings for the primary name server which overrides the value under the ansible group. This is how you set different values for the name servers. The host `ns0` is the primary name server in this example, with an IP address of `192.168.1.99`.

The `vars_files` used in this example shows how to split up the variables for the resource records into different files. This is not required, as seen in `bind9_single_server.md`, but can help with maintaining configurations that either have many zones or many resource records per zone.

## Example Playbook using `vars_files`

This example shows the `name_server_playbook.yml` that reads other files for the resource records and sets other variables using `group_vars` and `host_vars` files. See the `zone_vars.md` file for example file contents set under `vars_files:`.

```yaml
---
- name: Configure a single standalone Bind9 Name Server.
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
