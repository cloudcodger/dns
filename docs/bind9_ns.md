# The standard name servers configuration with Primary and Secondary DNS servers with special NS records

Similar to `bind9_standard_servers.md` but setting the NS records for each zone to specific values.

This document only explains the difference from the standard example to make this change and they only apply to the primary name servers configuration.

Notice the additional `ns` key under each zone. When not specified, the default will create NS records for each host in the `ansible_play_batch`. If you want to not include the primary name server in the list of NS records, you must set this to a list of host names that get expanded to `<host>.<domain_name>.` or a list of FQDNs (which must include a `.` at the end).

## Example Playbook using `vars_files`

This example only shows the `host_vars/ns0.yml` contents as could be use with the playbook and `group_vars` from the standard example.

`host_vars/ns0.yml` file (primary).

```yaml
---
bind9_servers_zones:
  - name: example.com
    ns:
      - ns2
      - ns3
    records_var: zone_example_com
  - name: "1.168.192.in-addr.arpa"
    ns:
      - ns2
      - ns3
    records_var: zone_192_168_1
```

This example shows setting the NS records such that they point to a FQDN of a load balancer.

```yaml
---
bind9_servers_zones:
  - name: example.com
    ns:
      - nslb.example.com.
    records_var: zone_example_com
  - name: "1.168.192.in-addr.arpa"
    ns:
      - nslb.example.com.
    records_var: zone_192_168_1
```
