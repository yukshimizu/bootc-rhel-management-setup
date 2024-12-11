Role Name
=========

This role "builder" is designed to create a builder VM on AWS EC2 for demo purpose.

Requirements
------------

Basically, the role assumes to setup a builder VM using Red Hat Cloud Access Gold Images on AWS EC2. 

The tested environment:
- RHEL-9.4_HVM_GA-20240827-x86_64-0-Access2-GP3


Role Variables
--------------

The following variables should be supplied when using the role. In this project, upper playbooks are supposed to set these variables.
- rhsm_username
- rhsm_passwd


Dependencies
------------

The following collections need to be installed beforehand. You can install those collections by using requirements.yml located in the project top directry.
- redhat.rhel_system_roles

Example Playbook
----------------

    - name: Configure builder server
      hosts: builder
      gather_facts: true
      vars:
        builder_private_dns_name: "private dns name of builder"

      vars_prompt:
        - name: rhsm_username
          prompt: "What is your Red Hat login name?"
          private: false
        - name: rhsm_passwd
          prompt: "What is your Red Hat login password?"

      roles:
        - builder


License
-------

MIT

Author Information
------------------

Yukiya Shimizu
https://github.com/yukshimizu
