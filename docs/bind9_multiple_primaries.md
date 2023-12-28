# Non-standard example where different name servers are configured as Primary for different zones

A complex example for special cases where each zone is given a different host as the primary name server. Also allowing multiple secondary name servers for the zones. This tries to indicate how complex you can make your configuration using this role.

Configuring multiple DNS servers like this requires that the `bind9_servers_zones` _must_ be set in _multiple_ places. Because each name server acting as primary for a zone will require a unique value, these will need to be set in `host_vars` files for each of the hosts. Any name servers acting only as secondaries can either all be configured using a `group_vars` file or individually in additional `host_vars` files.

A special note about the `ns` key under each zone is important here. The default is to create an NS records for each host, this could make a resolver looking up `ctrl1.example.com` query `ns3.example.com` name server for the result. Since that server is not configured as either the primary or secondary for that zone, it would not be able to resolve it. By setting the NS records to only `ns5` and `ns6`, the resolvers would only query those servers for records under that domain. Explaining why is outside the scope of this document.

## Example Playbook

This example shows the `name_server_playbook.yml` that reads other files for the resource records and sets other variables using `group_vars` and `host_vars` files. See the `zone_vars.md` file for example file contents set under `vars_files:`.

This assumes that the Ansible grupu `name_server` contains the hosts `ns2`, `ns3`, `ns4`, `ns5` and `ns6` with IP addresses as found in the `zone_vars.md` file. The name servers will function as follows:

- `ns2` : primary for `example.com` and `1.168.192.in-addr.arpa`
- `ns3` : primary for `second.example.com`
- `ns4` : primary for `third.example.com`
- `ns5` and `ns6` : secondaries for all zones

```yaml
---
- name: Configure a multiple primary Bind9 Name Servers.
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
      - "192.168.1.2"
  - name: second.example.com
    primaries:
      - "192.168.1.3"
  - name: third.example.com
    primaries:
      - "192.168.1.4"
  - name: "1.168.192.in-addr.arpa"
    primaries:
      - "192.168.1.2"
```

`host_vars/ns2.yml` file (primary).

```yaml
---
bind9_servers_zones:
  - name: example.com
    ns:
      - ns5
      - ns6
    records_var: zone_example_com
  - name: "1.168.192.in-addr.arpa"
    ns:
      - ns5
      - ns6
    records_var: zone_192_168_1
```

`host_vars/ns3.yml` file (primary).

```yaml
---
bind9_servers_zones:
  - name: second.example.com
    ns:
      - ns5
      - ns6
    records_var: zone_second_example_com
zone_second_example_com:
  - 'pve1;;A;192.168.1.251'
  - 'pve2;;A;192.168.1.252'
  - 'pve3;;A;192.168.1.253'
```

`host_vars/ns4.yml` file (primary).

```yaml
---
bind9_servers_zones:
  - name: third.example.com
    ns:
      - ns5
      - ns6
    records_var: zone_third_example_com
zone_third_example_com:
  - 'pve1;;A;192.168.1.251'
  - 'pve2;;A;192.168.1.252'
  - 'pve3;;A;192.168.1.253'
```
