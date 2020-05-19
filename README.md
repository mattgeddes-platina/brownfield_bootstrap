# Ansible Collection - mattgeddes_platina.brownfield_bootstrap

## Overview

This small Ansible collection helps to orchestrate preparation of a so-called
_brownfield_ node (one that already has an operating system installed) in order
for it to be able to be added to, and managed by, Platina Command Center (PCC).

## Deployment Steps

This process assumes one or more nodes that have an operating system and are
new to PCC, and that there's some form of username/password authentication
required to log into the node over the SSH protocol.

At a high level, the steps provided will walk you through:
1. Using `ansible-galaxy` to install a collection that wraps up a simple playbook.
1. Using `ansible-galaxy` to install roles needed by the collection.
1. Using `ansible-vault` to create an encrypted store for the credentials needed for the nodes.
1. Using `ansible-playbook` to execute the collection's playbook, which will create a user, trust its key and grant passwordless sudo access, whilst also ensuring that dependencies are installed (such as Python).

### Prerequisites

Make sure you have a recent version of Ansible installed. This can be run from any node that has reachability to the nodes you wish to manage. The PCC node itself is a good candidate.

None of the commands provided need to be (or should be) run as root. An unprivileged user is preferred.

### Specific Steps

1. Install the Ansible Galaxy Collection with this command: `ansible-galaxy install mattgeddes_platina.brownfield_bootstrap -p .`
1. Change directory: `cd ./mattgeddes_platina/brownfield_bootstrap`
1. Install dependencies: `ansible-galaxy install -r requirements.yml`
1. Modify the file called `inventory` to include the nodes to be managed, grouped by common initial/default credentials. A sample is provided. Add as many as you need.
1. Create encrypted vault for plaintext passwords from the template provided:
  1. Create a new vault (the file called `group_vars/all`) and provide an encryption key/password: `ansible-vault create group_vars/all`
  1. This will open a text editor and allow you to specify the contents of this vault. A simple template of this file has been created, along with helpful comments and examples. Simply paste in the contents of the `vars_template.yml` file into this editor window, replace the example values with yours, and Save.
1. Run the playbook: `ansible-playbook -i ./inventory --ask-vault-pass ./playbook.yml`

The playbook will ask for the password you used to create the vault.

The playbook should run through to completion with each of the nodes now being able to be added to PCC.

### SSH keys and passphrases

This module only allows for cases where there is a username and password for initial login to the node. In cases where there is (also) an ssh private key and that private key requires a passphrase, use ssh key forwarding. On most systems, that should be as simple as:

1. Running: `eval ``ssh-agent```
1. Running: `ssh-add ./id_rsa`

Where `./id_rsa` should be the complete path to the private key to use. The `ssh-add` command will ask for the passphrase and Ansible will be able to manage the nodes using that key when executed from that shell.

