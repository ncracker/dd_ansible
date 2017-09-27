# Datadog + Ansible

### Brief History of Everything

The systems administrator’s role has been evolving rapidly in the last decade and with it the number of tools that we use has also grown exponentially.  Trying to stay on top of it has ushered the era of both monitoring and configuration management, the latest iterations of which are  Datadog and Ansible.

Datadog is a SaaS monitoring platform for cloud-scale applications that brings together data from infrastructure, applications, databases and services to present a unified view of an entire stack. Datadog can collect data directly from your cloud provider of choice, but its full power lies in its agent and the many application integrations that surround it. Datadog provides an Ansible role that's available in the galaxy and this is  what we're using in this project.

Unlike previous configuration management solutions Ansible follows the push method. It does not require an agent on the managed nodes and there's no concept of “master”. Its dependencies are SSH and python 2.7, which are available out-of-the-box on most modern Linux distributions. Its configuration files (playbooks) are in YAML and follow simple top to bottom flow. All this translates to ease of use and quick setup time and has made it a favorite for many SRE teams.

We're now going to demonstrate how easy it is to deploy the Datadog agent with the help of Ansible. We're also going to see our progress as we do this via the Datadog events stream.

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
3. Replace the example Datadog api key in `./playbooks/dd_agent.yml` and `./playbooks/callback_plugins/datadog_callback.yml` with a valid key from your Datadog account - you can find it in https://app.datadoghq.com/account/settings#api.

4. You're now ready to execute your first Ansible command, but do make sure you've loaded you ssh-key so you can access the remote node (`ssh-add`)
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
This indicated that you were able to successfully run Ansible against the your node.

5. The only thing left to do is run an actual Ansible playbook, which it turn will call the Datadog role and install the dd-agent on your node(s)
```
cd playbooks
ansible-playbook dd_agent.yml
```
The output of the command will give you details of the individual tasks being performed. It will also generate two events in your Datadog events stream, one indicating the a playbook run was started and one for its completion. Any errors will also be displayed if they occur during the run of the playbook.

<img src="https://c1.staticflickr.com/5/4436/37099840420_8ed4889edb_b.jpg" width="656" height="156" alt="Ansible events in Datadog">

This indicates Ansible began the execution of the playbook and completed it. Our datadog agent was

## Final thoughts
I hope you found the information and example useful. Please do not hesitate to reach out with comments or suggestions.
Package dependencies can also be installed with `pip install -r requirements.txt`.

## References
For more information on both Datadog and Ansible please refer to the sources below
Datadog - https://www.datadoghq.com/
Monitor your automation, automate your monitoring - https://www.datadoghq.com/blog/ansible-datadog-monitor-your-automation-automate-your-monitoring/
The official Datadog Ansible role - https://github.com/DataDog/ansible-datadog (included here in `./roles/Datadog`)
Datadog's Ansible callback repo - https://github.com/DataDog/ansible-datadog-callback (included here in `./playbooks/callback_plugins`)
Ansible's official documentation page - http://docs.ansible.com/ansible/latest/index.html
