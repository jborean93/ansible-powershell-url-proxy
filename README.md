# Ansible Windows URL Credential and Proxy Tests

Create a test environment that we can use to test out the credential and proxy
handling of Ansible's `Ansible.ModuleUtils.WebRequest.psm1` shared web util.


## Requirements

* Vagrant
* VirtualBox
* Internet connection


## How to run

Change to the current directory and run `vagrant up`. This will set up the
environment ready to test. To run the tests you can run the following;

```
vagrant ssh ANSIBLE
cd ~/proxy-tests
ansible-playbook -i inventory.yml tests.yml
```

_Note: If you are testing changes you will have to checkout the code and activate the env manually._

You can also run the test playbook locally but you would need to ensure;

* You can resolve the FQDN of `dc.ansible.proxy` and `test.ansible.proxy` to the actual IP address
* Can connect using Kerberos auth `kinit vagrant-domain@ANSIBLE.PROXY`

This is typically achieved by having a DNS resolver for the domain realm to the
domain controller. On a linux host it is simplest to use the `dnsmasq` resolver
for Network Manager. The `main.yml` playbook shows you how this can be setup.


## Environment

Running `vagrant up` will setup the following hosts;

* Windows Server 2019 as a domain controller
* Windows Server 2019 with an IIS site protected by Windows authentication
* Windows Server 2019 site to be used as the test host, just domain joined
* Centos 7 host running multiple instances of Squid with the following ports:
    * `3129`: No authentication required
    * `3130`: Basic auth with simple user/pass defined in `inventory.yml`
    * `3131`: Basic auth with domain credentials backed by LDAP
    * `3132`: Kerberos auth
* Centos 7 host to run the actual Ansible tests on
