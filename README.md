
# Ansible Role - potos\_firewall 

Role to configure firewall rules of Potos Linux Clients.

[![Test](https://github.com/projectpotos/ansible-role-potos_firewall/actions/workflows/test.yml/badge.svg)](https://github.com/projectpotos/ansible-role-potos_firewall/actions/workflows/test.yml)

## Example Playbook

As this role is tested via Molecule, one can use [that
playbook](./molecule/default/converge.yml) as a starting point:

```yaml
---

- name: Converge
  hosts: all
  gather_facts: yes
  vars:
    potos_firewall_package_state: 'latest'
    potos_firewall_rules:
      - rule: allow
        from_ip: 123.123.123.123
        to_port: 124
        protocol: tcp
        comment: "allow access to port 124"
  tasks:
    - name: run role
      ansible.builtin.include_role:
        name: 'ansible-role-potos_firewall'

```

## Role Variables

The default variables are defined in [defaults/main.yml](./defaults/main.yml).


```yaml
---
# to define if ufw or firewalld should be used
potos_firewall_force_type: 'none'

# to disable ufw
potos_firewall_disable_ufw: 'false'

# to define default Firewall policies
potos_firewall_incoming_default_policy: 'reject'
potos_firewall_outgoing_default_policy: 'allow'

# to define loglevel
potos_firewall_loglevel: 'on'

# to define to define state of the firewall package
potos_firewall_package_state: 'present'

# to define firewall rules as a list
potos_firewall_rules: []
# Each rule can be defined with the following:
  # define action of the rule "allow, deny, reject, limit"
  rule:
  # define an interface for the rule
  interface:
  # define the direction of the rule "in, out, routed"
  direction: in
  # define source IP address
  from_ip:
  # define destination IP address
  to_ip: 
  # define source port
  from_port:
  # define destination port
  to_port:
  # define protocol "any, tcp, udp, ipv6, esp, ah"
  protocol: any
  # apply the rule to routed/forwarded packets
  route: false
  # log new connections matched to this rule
  log: false
  # add a comment to the rule
  comment: "{{ item.comment | default(omit) }}"
  # delete the rule
  delete: "{{ item.delete | default(omit) }}"
```

For example:
```yaml
potos_firewall_rules:
  - rule: allow
    from_ip: 123.123.123.123
    to_port: 124
    protocol: tcp
    comment: "allow access to port 124"
  - rule: allow
    to_ip: 8.8.8.8
    direction: out
    to_port: 53
```
Creates the following ufw configuration:
```
# ufw status
Status: active

To                         Action      From
--                         ------      ----
124/tcp                    ALLOW       123.123.123.123            # allow access to port 124

8.8.8.8 53                 ALLOW OUT   Anywhere 
```
If a rule should be changed or removed, `delete: true` has to be added, to delete the rule. After that, `delete: true` can be removed and the rule changed or removed entirely. If changed, it will then be added to the ufw configuration as a new rule.
```yaml
potos_firewall_rules:
  - rule: allow
    to_ip: 8.8.8.8
    direction: out
    to_port: 53
    delete: yes
```


## Requirements
```
ansible-galaxy collection install community.general
```

## License

See [LICENSE](./LICENSE)

## Author Information

[Project Potos](https://github.com/projectpotos)
