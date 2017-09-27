# Datadog + Ansible

### History of Everything

The systems administrator’s role has been evolving rapidly in the last decade and with it the number of tools that we use has also grown exponentially.  Trying to stay on top of it has ushered the era of configuration management, the latest iteration of which is Ansible.

Unlike previous solutions Ansible follows the push method and not pull. It requires no agents on the managed nodes and no “master” or management node. Its dependencies are SSH and python 2.7, which are available out-of-the-box on most modern Linux distributions. Its configuration files (playbooks) are in YAML and follow simple top to bottom flow. All this translates to easy of use and quick setup time and has made it a favorite for many SRE teams.

## Installation

Installing Ansible is as easy as `pip install ansible`, but can also be installed via your distribution's package manager. This is done on the system you're going to be using to push from (control machine). You don't have to install Ansible on the systems you intend to manage. For more installation options on your specific platform see http://docs.ansible.com/ansible/latest/intro_installation.html

Once you have done that you can clone this example repo with `git clone git@github.com:ncracker/dd_ansible.git`

Our intended use case is deploying Datadog agents with Ansible, as well as posting to our Datadog events stream the results of Ansible playbook runs. This would not only deploy the Datadog agents for us, but also allow us to see successes and failures of any playbook runs. To do this, we also want to install the datadog python library with `pip install datadog`

## Getting Started
1. First we source our setenv file, which will tell Ansible where our host file lives (the file that contains the nodes we want to manage `./hosts`) as well as our ansible configuration file (`./etc/ansible.cfg`)
```
cd dd_ansible
source setenv
```
2. We also want to tell Ansible what the FQDNs or IPs of the nodes we want to manage are. Add that with echo or your favorite editor, e.g.
```
echo "ec2-14-223-54-111.us-east-1.compute.amazonaws.com" >> ./hosts
```
3. Replace the example Datadog api key in `./playbooks/nginx.yml'` and `./playbooks/callback_plugins/datadog_callback.yml` with a valid key from your Datadog account - you can find it in https://app.datadoghq.com/account/settings#api.

4. You're now ready to execute your first ansible command, but do make sure you've loaded you ssh-key so you can access the remote node (`ssh-add`)
```
ansible -m ping -i hosts all
```
You should see the following output, e.g.
```
ec2-14-223-54-111.us-east-1.compute.amazonaws.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

## References
The official Datadog Ansible role - https://github.com/DataDog/ansible-datadog (included here in `./roles/Datadog`)
Datadog's Ansible callback repo - https://github.com/DataDog/ansible-datadog-callback (included here in `./playbooks/callback_plugins`)
Ansible's official documentation page - http://docs.ansible.com/ansible/latest/index.html
