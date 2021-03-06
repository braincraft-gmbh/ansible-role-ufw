UFW Rules setup
##############

Ansible playbook rule for setting up UFW. With support for IPv4/IPv6 forwarding.

Requirements 
---------

Testing on Ubuntu 16.04

Variables
---------
- default.yml
Explanation:
```yaml


ufw_kernel_modules:  	       #Set iptables kernel modules
ufw_policy:  		       #Set default policy to deny/allow

ufw_forwarding: []             #IPv4 forwarding rules
  - comment:                   #Comment                  ; Not yet used.
    incoming_dev:             #Incomming interface
    incoming_network:         #Incomming network/subnet
    outgoing_dev:              #Outgoing interface
    outgoing_network:          #Outgoing network/subnet
    masquerading:              #Enable masquerading (boolean)
    conntrack_state:           #Incomming traffic ctstat setting
    reroute: []                #Reroute outgoing IP of host (Useful for internal subnets)
      - comment:               #Comment                  ; Not yet used.
        routed_ip:             #Reroute IP
        source_ip:             #Source is internal ip where is routed out
    forwards: []               #Port forwarding
      - comment:               #Comment                  ; Not yet used.
        protocols: []          #Protocol. TCP/UDP as array
        allowed_sources: []    #Allowed IPs   (TODO)
        incoming_ip:          #Orignal destiantion ip
        incoming_port:        #Orignal destiantion port
        destination_ip:        #Outgoing IP addresss
        destination_port:      #Outgoing Port addresss

ufw.forwarding6: []            #IPv4 forwarding rules
  - comment:                   #Comment                  ; Not yet used.
    incoming_dev:             #Incomming interface
    incoming_network:         #Incomming network/subnet
    outgoing_dev:              #Outgoing interface
    outgoing_network:          #Outgoing network/subnet
    conntrack_state:           #Incomming traffic ctstat setting
    masquerading:              #Enable masquerading (boolean)
    forwards: []               #Port forwarding
      - comment:               #Comment                  ; Not yet used.
        incoming_ip:          #Orignal destiantion ip   ; Only used when masquerading is enabled
        incoming_port:        #Orignal destiantion port ; Only used when masquerading is enabled
        protocols: []          #Protocol
        destination_ip:        #Outgoing IP addresss     
        destination_port:      #Outgoing Port addresss   

```

Custom rules
------------

If you want to add custom rules to you machine(s) just use UFW module of ansible:
http://docs.ansible.com/ansible/ufw_module.html

Example playbook
----------------

Simple example with custom ufw rules
```yaml
- name: Setup UFW 
  hosts: all
  roles:
    - ufw

- name: Custom UFW rules
  hosts: all
  tasks:
    - name: Allow OpenSSH on port 22
      ufw: rule=allow name=OpenSSH
    - name: Allow HTTP 
      ufw: rule=allow port=80 proto=tcp 
    - name: Allow HTTPS 
      ufw: rule=allow port=https proto=tcp 
    - name: Allow port 8443 from 1 host 
      ufw: rule=allow interface="{{ item.incoming_dev }}" direction=in proto=tcp src="2001:db8:1337:beef:cafe::1" to_port=8443
      with_items:
        - "{{ ufw.forwarding6 }}"
    
```


Licence
-------

MIT

Author
-------

Egbert Verhage <egbert@eggiecode.org>
