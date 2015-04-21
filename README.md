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

ansible-playbook -i static.inv projects/example/fwbuilder.yml -e "ENV=DEV HOSTGROUP=ANSIBLEAPI"
