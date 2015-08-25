# ansible-fwbuilder

An Ansible role that translates host, port & rule objects into an IPTables firewall script.

This role will help you to quickly define common firewall rules that you can deploy across all of your environments without duplication.

Features:

* Define multiple ports, hosts & protocols in a single rule
* Define port ranges
* Define host subnets
* Define a default action for all traffic
* Enable / disable logging of dropped packets
* Define common ruleset to apply to all hosts
* Define server / app specific rulesets to apply to select host groups

Example:

cd ansible-fwbuilder

- Run all tasks and install firewall scripts (Redhat & Debian)
ansible-playbook -i static.inv projects/example/fwbuilder.yml

- Generate an IPTables script and output to /tmp/iptables
ansible-playbook -i static.inv projects/example/fwbuilder.yml --limit=local --tags=create-rules

A file containing the IPTable rule output can be found in /tmp/iptables or /etc/default/iptables
