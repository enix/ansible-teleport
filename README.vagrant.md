Test ansible role using Vagrant
=====================

Ansible is a great tool, and trying things out with a virtual-machine is a
great way to learn, try things out, and test changes. But if you're trying
to learn Ansible and Vagrant/Virtualbox all at once, configuring a test-setup
just right can be a steep learning curve. This template demonstrates how
to set-up Vagrant to test your new Ansible role.


tl;dr:
------

To startup tests simply run:

    cd vagrant/
    vagrant up

In case you want to relaunch the tests, restart vagrant provision
    vagrant provision

For debugging purpose vagrant provide an easy way to connect inside the VM
    vagrant ssh


Notes
------
*Vagrantfile* sets up a testing virtual-machine,
by default named `debianjessie64` (see [Vagrantfile](vagrant/Vagrantfile)).
This should be renamed to something sensible for your role so you can
recognise it if you have multiple VM's up and running, e.g. when running::

    vagrant global-status

... to remind yourself where all your RAM went.

The *ansible.cfg* file tells ansible how to find the Vagrant SSH login
details, which makes it easy to run Ansible manually against your
VM via the command line, rather than indirectly
by re-running Vagrant commands - this can speed up testing.

Requirements
------------

[Vagrant-cachier](http://fgrehm.viewdocs.io/vagrant-cachier/) is recommended,
but optional.

To retrieve a copy of the roles listed in a requirements file, run::

    ansible-galaxy install -r requirements.yml

or

    ansible-galaxy install --force -r requirements.yml

to forcibly update old copies.

Note that *[ansible.cfg](vagrant/ansible.cfg)* is configured such that
roles installed via Ansible Galaxy will be installed under
*vagrant/galaxy_roles*.

When documenting a role, you should either specify expected
pre-requisites (e.g. git) in the README, or if your dependencies
are provided by a specific role then you should record it in the
role metadata ([see docs](https://galaxy.ansible.com/intro#dependencies)).

Openstack provider
------------------

To use the Openstack provider you must install and configure a specific
plugin to Vagrant.

First need to install [vagrant-openstack-provider](https://github.com/ggiamarchi/vagrant-openstack-provider).

Then some environment source files must be configured:
  * `~/openrc-common.sh`

```bash
#!/bin/bash

export OS_IMAGE_API_VERSION=2
export OS_REGION_NAME=r1

export OS_FLOATING_IP_ALWAYS_ALLOCATE=false
export OS_FLOATING_IP_POOL_NAME='Public Floating'
export OS_FLOATING_IP_POOL_ID=1111
export OS_FLOATING_IP_POOL=${OS_FLOATING_IP_POOL_NAME}

export OS_FLAVOR=GP1.XS
export OS_IMAGE='Debian 9.5.5'
export OS_NETWORK=net
export OS_SSH_USERNAME=debian
```
  * `~/enix_$login-openrc.sh`

```bash
#!/usr/bin/env bash
# To use an OpenStack cloud you need to authenticate against the Identity
# service named keystone, which returns a **Token** and **Service Catalog**.
# The catalog contains the endpoints for all services the user/tenant has
# access to - such as Compute, Image Service, Identity, Object Storage, Block
# Storage, and Networking (code-named nova, glance, keystone, swift,
# cinder, and neutron).
#
# *NOTE*: Using the 2.0 *Identity API* does not necessarily mean any other
# OpenStack API is version 2.0. For example, your cloud provider may implement
# Image API v1.1, Block Storage API v2, and Compute API v2.0. OS_AUTH_URL is
# only for the Identity API served through keystone.
export OS_AUTH_URL=https://identity.api.r1.nxs.enix.io/v2.0/
# With the addition of Keystone we have standardized on the term **tenant**
# as the entity that owns the resources.
export OS_TENANT_ID=$MYTENANTID
export OS_TENANT_NAME="enix/$LOGIN"
# unsetting v3 items in case set
unset OS_PROJECT_ID
unset OS_PROJECT_NAME
unset OS_USER_DOMAIN_NAME
unset OS_INTERFACE
# In addition to the owning entity (tenant), OpenStack stores the entity
# performing the action as the **user**.
export OS_USERNAME="$LOGIN"
# With Keystone you pass the keystone password.
echo "Please enter your OpenStack Password for project $OS_TENANT_NAME as user $OS_USERNAME: "
read -sr OS_PASSWORD_INPUT
export OS_PASSWORD=$OS_PASSWORD_INPUT
# If your configuration has multiple regions, we set that information here.
# OS_REGION_NAME is optional and only valid in certain environments.
export OS_REGION_NAME="r1"
# Don't leave a blank variable, unset it if it was empty
if [ -z "$OS_REGION_NAME" ]; then unset OS_REGION_NAME; fi
export OS_ENDPOINT_TYPE=publicURL
export OS_IDENTITY_API_VERSION=2

source ~/openrc-common.sh
```

Then to use it you must load those ressources files and use the openstack provider

```
. ~/enix_$login-openrc.sh
vagrant up --provider openstack
```

If you need to debug you can enable all requests logging setting `VAGRANT_LOG=debug` or `VAGRANT_OPENSTACK_LOG=debug` environment variable.


License
-------
GPLv2
