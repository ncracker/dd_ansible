### History of Everything

The systems administrator’s role has been evolving rapidly in the last decade and with it the number of tools that we use has also grown exponentially.  Trying to stay on top of it has ushered the era of configuration management, the latest iteration of which is Ansible.

Unlike previous solutions Ansible follows the push method and not pull. It requires no agents on the managed nodes and no “master” or management node. Its dependencies are SSH and python 2.7, which are available out-of-the-box on most modern Linux distributions. Its configuration files (playbooks) are in YAML and follow simple top to bottom flow. All this translates to easy of use and quick setup time and has made it a favorite for many SRE teams.

## Installation

Installing ansible is as easy as `pip install ansible`, but can also be installed via your distribution's package manager. This is done on the system you're going to be using to push from (control machine). You don't have to install ansible on the systems you intend to manage. For more installation options see http://docs.ansible.com/ansible/latest/intro_installation.html
