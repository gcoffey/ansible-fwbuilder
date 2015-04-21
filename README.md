# ansible-fwbuilder

An Ansible role that translates objects into IPTables firewall rules.

This role will help you to quickly define common firewall rules that you can deploy across all of your environments without duplication.

Features:

* Define multiple ports, hosts & protocols in a single rule
* Define port ranges
* Define host subnets
* Define a default action for all traffic
* Enable / disable logging of dropped packets
* Define common ruleset to apply to all hosts
* Define server / app specific rulets to apply to select host groups

Examples.

* Defining Network Interfaces (per server).
nic:
  mgmt: eth0

* Defining Hosts (per environment).
fw_hosts:
  all:
    - "0.0.0.0/0"
  ansible:
    - "192.168.0.100"
  amazon_ec2:
    - "178.236.6.52"
    - "103.246.150.224"
  loadbalancer:
    - "192.168.0.2"
  monitoring:
    - "192.168.0.110"
    - "192.168.0.120"
  nameservers:
    - "192.168.0.10"
    - "192.168.1.10"
  proxy_out:
    - "192.168.0.250"
  trusted_hosts:
    - "192.168.2.0/24"
    
* Defining Common Ports.
fw_port:
  any: { ports: [ all ], proto: [ tcp, udp, icmp ] }
  ansible_api: { ports: [ 8080, 8081, 8181 ], proto: [ tcp ] }
  dns: { ports: [ 53 ], proto: [ tcp, udp ] }
  http: { ports: [ 80 ], proto: [ tcp ] }
  https: { ports: [ 443 ], proto: [ tcp ] }
  monitoring_tcp: { ports: [ 22, 80, 443 ], proto: [ tcp ] }
  monitoring_udp: { ports: [ 161, 25 ], proto: [ udp ] }
  ping_reply: { proto: icmp, type: "echo-reply" }
  ping_request: { proto: icmp, type: "echo-request" }
  proxy_out: { ports: [ 8080, 8443 ], proto: [ tcp ] }
  ssh: { ports: [ 22 ], proto: [ tcp ] }

* Defining Common Rules.
fw_base:
  default_action: DROP
  log_dropped: True
  allow_in:
    - { nic: "{{ nic.mgmt }}", src: "{{ fw_hosts.ansible }}", port: "{{ fw_port.ssh }}" }
    - { nic: "{{ nic.mgmt }}", src: "{{ fw_hosts.trusted_hosts }}", port: "{{ fw_port.ssh }}" }
    - { nic: "{{ nic.mgmt }}", src: "{{ fw_hosts.monitoring }}", port: "{{ fw_port.monitoring_tcp }}" }
    - { nic: "{{ nic.mgmt }}", src: "{{ fw_hosts.monitoring }}", port: "{{ fw_port.monitoring_udp }}" }
    - { nic: "{{ nic.mgmt }}", src: "{{ fw_hosts.monitoring }}", port: "{{ fw_port.ping_request }}" }
  allow_out:
    - { nic: "{{ nic.mgmt }}", dest: "{{ fw_hosts.nameservers }}", port: "{{ fw_port.dns }}" }
    - { nic: "{{ nic.mgmt }}", dest: "{{ fw_hosts.proxy_out }}", port: "{{ fw_port.proxy_out }}" }
    - { nic: "{{ nic.mgmt }}", dest: "{{ fw_hosts.monitoring }}", port: "{{ fw_port.ping_reply }}" }

* Additional Rules (per server).
fw_extra:
  allow_in:
    - { nic: "{{ nic.mgmt }}", src: "{{ fw_hosts.loadbalancer }}", port: "{{ fw_port.ansible_api }}" }
  allow_out:
    - { nic: "{{ nic.mgmt }}", dest: "{{ fw_hosts.amazon_ec2 }}", port: "{{ fw_port.https }}" }
