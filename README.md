ansible-swift
=============
Deploy multi data and proxy nodes swift cluster in vagrant environment using ansible.

ansible-playbook  -vvvv -i tests/inventories/deploy_hosts deploy_swift.yml -e target_env=vagrant

**Minimal testing with centos6.5 on vagrant virtualbox**

Key feature:
- Adds mutiple disk in vagrant store vm (not jus loop back disk)
- ring file are created and pushed during run. Not just prepopulated ring.
- modify vagrant.yml to change number of nodes in cluster.
- Number of disks, ring variable etc configurable using group_vars/all 

