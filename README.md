# connect-2019 | Series of playbooks and files for Cisco Connect 2019 DC Automation
The purpose of this repository is to act as a collection of sample code to get started with simple DC network automation using NX-OS devices.  This is not meant to serve as a full instruction set, but to augment the original discussion.

## Vagrantfile
A batteries-included Vagrant file to instantiate two Nexus 9000v switches.  Management of the switches is exposed through the standard NAT from the virtualization software, with additional ports exposed for HTTP/HTTPS management of devices through NXAPI.  This interface is referenced as `mgmt0` inside of the switch config.
Data-plane interfaces are located on E1/1 of each device, which can be used to test automations against.  Keep in mind that the Vagrant machines have no concept of "multimachine" and require higher-order operations (e.g. through Vagrant or manual config) to prevent duplicate MAC addresses on each machine.  Because of the nature of the n9000v, it may be required to add in a static MAC address on one of the data-plane interfaces, like so

```
interface Ethernet1/1
  no switchport
  mac-address 0800.276c.aa16
  ip address 1.1.1.2/24
  no shutdown
```

in order for traffic to pass between the devices accordingly.

Exposed ports are given in the table

| Device | Protocol | Port |
| ------ | -------- | ---- |
| n9kv1 | HTTP | 8080 |
| n9kv1 | HTTPS | 8443 |
| n9kv2 | HTTP | 8081 |
| n9kv2 | HTTPS | 8444 |

Also keep in mind that the `Vagrantfile` will need to be edited with the respective name of the imported `.box` image.
Due to shell differences, it may be required to instantiate each VM independently using
```
vagrant up n9kv1
vagrant up n9kv2
```

## Usage
Playbooks designed around (2) NXOS9000v hosts provisioned in VIRL.  Management interfaces are tied to FLAT network, configured using "infrastructure only" mode within VIRL.  E1/1 and E1/2 interfaces are connected between the switch pair.  0/0 route for management traffic is configured.  All other configuration is default on the devices.

## Ansible Files

A rough breakdown of the included files will be given below.  These files can additionally be used inside of the Vagrant environment defined by the Vagrantfile above.  All files have been refactored to use the new (as of Ansible 2.5) `network_cli` connection method.  However, in some instances, it is desirable to leverage NXAPI for comparison purposes.  For these playbooks, the connection is still `local` in order to leverage the `provider` statements in each module.

### config_tool.yaml
Simple Ansible playbook used to generate sample configurations based on inputs from variables located at the top of the playbook.
Configuration generation based on Jinja2 template located in the `templates` folder.  This can be modified to have desired output of configuration.

This playbook illustrates simple "config generation" automation for those who aren't comfortable with machines programming machines.

### vault.yml
Encrypted file using `ansible-vault` to carry WebEx Teams (née: Spark) room token information to test access to the API.

### spark_test.yaml
Simple playbook to test posting a message to a user via the WebEx Teams API.  Leverages the encrypted vault file for the ID of the "bot" used to post the message.  In order to leverage this -- the "token" of the specific user or bot will need to be put into a new vault or placed as plain-text in the playbook.

### n9kv*.yaml
Several different Ansible playbooks, roles, etc to help in conveying the power of Ansible usage in having automation discussions with network engineering staff.  Playbooks progress in complexity, from very linear, single purpose playbooks using CLI/SSH transport -- to more complex playbooks with conditional elements, encrypted information in Vault, and calls to external messaging services ("ChatOps") to indicate completion of device provisioning.

### revert.yaml
Playbook to push the Nexus 9000v devices back to standard configuration (rollback config pushed by the `n9kv*` playbooks).
