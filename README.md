### History of Everything

The systems administrator’s role has been evolving rapidly in the last decade and with it the number of tools that we use has also grown exponentially.  Trying to stay on top of it has ushered the era of configuration management, the latest iteration of which is Ansible.

Unlike previous solutions Ansible follows the push method and not pull. It requires no agents on the managed nodes and no “master” or management node. Its dependencies are SSH and python 2.7, which are available out-of-the-box on most modern Linux distributions. Its configuration files (playbooks) are in YAML and follow simple top to bottom flow. All this translates to easy of use and quick setup time and has made it a favorite for many SRE teams.

## Installation

Installing Ansible is as easy as `pip install ansible`, but can also be installed via your distribution's package manager. This is done on the system you're going to be using to push from (control machine). You don't have to install Ansible on the systems you intend to manage. For more installation options on your specific platform see http://docs.ansible.com/ansible/latest/intro_installation.html

Once you have done that you can clone this example repo with `git clone git@github.com:ncracker/dd_ansible.git`

Our intended use case is deploying Datadog agents with Ansible, as well as posting to our Datadog events stream the results of Ansible playbook runs. This would not only deploy the Datadog agents for us, but also allow us to see successes and failures of any playbook runs. To do this, we also want to install the datadog python library with `pip install datadog`

## Getting Started


## References
The official Datadog Ansible role - https://github.com/DataDog/ansible-datadog (included here in `./roles/Datadog`)
Datadog's Ansible callback repo - https://github.com/DataDog/ansible-datadog-callback (included here in `./playbooks/callback_plugins`)
Ansible's official documentation page - http://docs.ansible.com/ansible/latest/index.html
