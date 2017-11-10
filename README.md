# Datadog + Ansible

### Brief History of Everything

The systems administrator’s role has been evolving rapidly in the last decade and with it the number of tools that we use has also grown exponentially.  Trying to stay on top of it has ushered the era of both monitoring and configuration management, the latest iterations of which are  Datadog and Ansible.

Datadog is a SaaS monitoring platform for cloud-scale applications that brings together data from infrastructure, applications, databases and services to present a unified view of an entire stack. Datadog can collect data directly from your cloud provider of choice, but its full power lies in its agent and the many application integrations that surround it. Datadog provides an Ansible role that's available in the galaxy and this is what we're using in this project.

Unlike many other configuration management solutions Ansible follows the push method. It does not require an agent on the managed nodes and there's no concept of “master”. Its dependencies are SSH and python 2.7, which are available out-of-the-box on most modern Linux distributions. Its configuration files (playbooks) are in YAML and follow simple top to bottom flow. All this translates to ease of use and quick setup time and has made it a favorite for many SRE teams. Ansible also provides the notion of "roles" which are intended to be used as shippable modules. Think Puppet modules or Chef cookbooks. Often times the work you need to get done, say installing a particular database and configuring its service, has already been done by someone in the open source community and shared out via the Ansible Galaxy (the ansible repository of roles - see https://galaxy.ansible.com/explore#/). This greatly reduces the amount of time you need to get started. The example above, installing a database, is then reduced to installing that particular role (or pulling its repository into your Ansible ./roles directory) and writing a simple playbook to call this role. 

We're now going to demonstrate how easy it is to deploy the Datadog agent with the help of Ansible. We're also going to see our progress as we do this via the Datadog events stream.

## Installation

Installing Ansible is as easy as `pip install ansible`, but it can also be installed via your distribution's package manager. This is done on the system you're going to be using to push from (control machine). You don't have to install Ansible on the systems you intend to manage. For more installation options on your specific platform see http://docs.ansible.com/ansible/latest/intro_installation.html. Installing Ansible roles is usually achieved with the `ansible-galaxy` command, but here we have the datadog role already included in the repository so that won't be required for this tutorial. 

Our intended use case is deploying Datadog agents with Ansible as well as posting to our Datadog events stream the results of Ansible playbook runs, which we'll achieve by using the datadog callback Ansible plug-in, also included in the repo. This would not only deploy the Datadog agents for us, but also allow us to see successes and failures of any playbook runs within our Datadog Events Stream. To do this, we also want to install the datadog python library with `pip install datadog` and pyyaml with `pip install pyyaml`.

Once you have done that you can clone this example repo with `git clone git@github.com:ncracker/dd_ansible.git`.

The repository contains both a local copy of the Datadog role as well as the plugin that will post events to Datadog, located in `./roles/datadog` and `./playbooks/callback_plugins/` respectively. 

## Getting Started
1. First we source our setenv file, which will tell Ansible where our host file lives (the file that contains the nodes we want to manage `./hosts`), where to look for installed roles as well as our ansible configuration file (`./etc/ansible.cfg`)
```
cd dd_ansible
source setenv
```
2. We also want to tell Ansible what the FQDNs or IPs of the nodes we want to manage are. In our example we use a single Ubuntu 14 instance as a node. We add the instance fqdn to the ./hosts file with echo
```
echo "yourinstance.fqdn.name" >> ./hosts
```
3. Replace the example Datadog api key in `./playbooks/dd_agent.yml` and `./playbooks/callback_plugins/datadog_callback.yml` with a valid key from your Datadog account - you can find it in https://app.datadoghq.com/account/settings#api. This will allow the plugin to post the results of your playbook runs into your Datadog events stream.

4. You're now ready to execute your first Ansible command, but please make sure you can access the node you want to push to via ssh and you've loaded the ssh-key providing you that access (usually done with `ssh-add`). Once you're certain your access works go ahead and run 
```
ansible -m ping -i hosts mynodes
```
You should see the following output, e.g.
```
yourinstance.fqdn.name | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
This indicated that you were able to successfully run Ansible against the your node.

5. The only thing left to do is run an actual Ansible playbook, which it turn will call the Datadog role and install the dd-agent on your node(s)
```
cd playbooks
ansible-playbook dd_agent.yml
```
The output of the command will give you details of the individual tasks being performed. It will also generate two events in your Datadog events stream, one indicating the a playbook run was started and one for its completion. Any errors will also be displayed if they occur during the run of the playbook.

<img src="https://c1.staticflickr.com/5/4436/37099840420_8ed4889edb_b.jpg" width="656" height="156" alt="Ansible events in Datadog">

This indicates Ansible began the execution of the playbook and completed it. Our datadog agent was successfully deployed.

## How it happened
Opening our example playbook (dd_agent.yml) in your prefered editor will show you how we achieved the agent push.
```
---
- hosts: mynodes
  remote_user: ubuntu
  become: yes
  roles:
      - role: datadog
  vars:
    datadog_api_key: <Your API KEY>
    datadog_agent_version: 1:5.17.2-1
```
`- hosts:` defines the group of hosts we're pushing to. This matches the group name "mynodes" we had predefined for you in the `./hosts` file. All you did was to echo the FQDN at the end of the hosts file. 
`remote_user: ubuntu` indicates the username we've told ansible to use for this push. `become: yes` allows the playbook to elevate to sudo if necessary (required for package installs and service reboots). The following two lines tell our playbook to call the datadog role we have in our ./roles/ directory. That's then followed by two variables we pass to the datadog role, namely our api key we placed there earlier as well as the version of the agent we would like to push. Less than 10 lines of yaml playbook deployed the datadog agent for us. We could have pushed to many more systems if we had added those to our ansible hosts file.

Something worth pointing out is that you can call multiple roles from a single playbook, thereby installing your database while also deploying the datadog agent. Also Datadog role provides more advanced functionality as well - such as loading configuration files for your datadog integrations (like for that database you just deployed). Please refer to the role's documentation in the link below if you want to take advantage of these features.

## Final thoughts
Our example node was running Ubuntu, but you can easily deploy to other distributions. The only changes you would need to make are in `./etc/ansible.cfg` and in `./playbooks/dd_agent.yml` where we've specified the user Ansible is to use and well as the package version (`1:5.17.2-1` for apt-based platforms, use a `5.17.2-1` format on yum-based platforms). Again, please refer to The Official Datadog Ansible role in the references below for more information.

I hope you found the information and example useful. Please do not hesitate to reach out with comments or suggestions.
Package dependencies can also be installed with `pip install -r requirements.txt`.

All the information provided here is purely for reference. Please test extensively before using in production environments.

## References
For more information on both Datadog and Ansible please refer to the sources below
Datadog - https://www.datadoghq.com/
Monitor your automation, automate your monitoring - https://www.datadoghq.com/blog/ansible-datadog-monitor-your-automation-automate-your-monitoring/
The official Datadog Ansible role - https://github.com/DataDog/ansible-datadog (included here in `./roles/datadog`)
Datadog's Ansible callback repo - https://github.com/DataDog/ansible-datadog-callback (included here in `./playbooks/callback_plugins`)
Ansible's official documentation page - http://docs.ansible.com/ansible/latest/index.html
