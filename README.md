enix.teleport
=============

A role for deploying and configuring [teleport](https://goteleport.com) and extensions on unix hosts using [Ansible](http://www.ansible.com/).


Requirements
------------

Supported targets:

- Ubuntu 18.04 "Bionic"
- Ubuntu 20.04 "Focal"
- Debian 9 "Stretch"
- Debian 10 "Buster"
- Debian 11 "Bullseye"


Role Variables
--------------

This roles comes preloaded with almost every available default. You can override each one in your hosts/group vars, in your inventory, or in your play. See the annotated defaults in `defaults/main.yml` for help in configuration. All provided variables start with `teleport__`.

- `teleport__version` - Version of the binary to install and hold (default "" which install latest available in the repo, but can precise any debian package release version)

Dependencies
------------

- None

Usage
-----

Use Ansible galaxy requirements.yml

    # teleport from enix
    # private role
    - src: git+ssh://git@gitlab.enix.io/ansible/ansible-teleport.git
      name: enix.teleport


And add it to your play's roles:

    - hosts: all
      roles:
        - role enix.teleport:
            teleport__version: 8.1.0

You can also use the role as a playbook. You will be asked which hosts to provision, and you can further configure the play by using `--extra-vars`.

    $ ansible-playbook -i inventory --extra-vars='{...}' main.yml

Still to do
-----------

- Write the role itself, for one


Changelog
---------

### 0.1

Initial version.

License
-------

GPLv2

Author Information
------------------

Laurent Corbes <laurent.corbes@enix.fr> - http://www.enix.io
