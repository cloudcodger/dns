# Name Server configured for dynamic zone updates

An example where a zone is configured to allow dynamic updates.

A common use case would be when a DHCP server is configured to automatically update a zone when it assigns out IP addresses. The configuration required on the DHCP server is outside the scope of this document.

When using this playbook, all the records defined for `zone_dhcp_example_com` will get added directly to the zone file during the first run of the playbook. However, once it has been created, all updates must be performed using `nsupdate` and the role will check them using that module.

## Limitations

Currently removing an list item from a dynamic zones `records_var` will not remove the record from the zone as the module has no means of knowing if the record was added by something outside its control, like from a DHCP server. An administrator would need to manually remove such entries. Although, changes to a record will get performed.

There is no support for multiple values on the same record. Attempting to use the same record multiple times as in a static configuration will cause the last one to be set and all previous entries to get replaced by later ones. The "feature" would need to be added to the role.

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
        - name: dhcp.example.com
          allowed_tsig_keys:
            - dhcp.example.com.key
          records_var: zone_dhcp_example_com
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
      zone_dhcp_example_com:
        - 'router;;A;192.168.1.1'
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
