# Example of `zone_vars` files

These are examples fo files that could be placed under a `zone_vars` directory located at he same level as a playbook that references them. See the other files in this directory for examples of how they would be used.

`zone_vars/192.168.6.yml` file.

```yaml
zone_192_168_1:
  - '1;;PTR;router.example.com.'
  - '2;;PTR;ns2.example.com.'
  - '3;;PTR;ns3.example.com.'
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

`zone_vars/lab.codger.site.yml` file.

```yaml
zone_example_com:
  - 'ctrl1;;A;192.168.1.21'
  - 'ctrl2;;A;192.168.1.22'
  - 'ctrl3;;A;192.168.1.23'
  - 'dockreg;;A;192.168.1.4'
  - 'ns0;;A;192.168.1.99'
  - 'ns2;;A;192.168.1.2'
  - 'ns3;;A;192.168.1.3'
  - 'pve1;;A;192.168.1.251'
  - 'pve2;;A;192.168.1.252'
  - 'pve3;;A;192.168.1.253'
  - 'pxe;;A;192.168.1.9'
  - 'router;;A;192.168.1.1'
  - 'work1;;A;192.168.1.24'
  - 'work2;;A;192.168.1.25'
  - 'work3;;A;192.168.1.26'
  - 'zabbix;;A;192.168.1.8'
```
