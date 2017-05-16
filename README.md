# Apache NiFi and NiFi Toolkit Ansible Roles to create a multi-node secure NiFi cluster.

I've created 2 Ansible Roles ((chilcano.apache-nifi)[https://galaxy.ansible.com/chilcano/apache-nifi] and (chilcano.apache-nifi-toolkit)[https://galaxy.ansible.com/chilcano/apache-nifi-toolkit]) to automate the creation of a multi-node and secure NiFi cluster.

At the moment, the (chilcano.apache-nifi)[https://galaxy.ansible.com/chilcano/apache-nifi] Ansible Role doesn't implement (Cluster State coordination)[https://nifi.apache.org/docs/nifi-docs/html/administration-guide.html#state_management] through Apache ZooKeeper. It will be implemented in the next version of this Ansible Role.

For other side, I've implemented only (TLS Toolkit Standalone mode)[https://nifi.apache.org/docs/nifi-docs/html/administration-guide.html#tls-generation-toolkit] in the (chilcano.apache-nifi-toolkit)[https://galaxy.ansible.com/chilcano/apache-nifi-toolkit] Ansible Role.

Further details and samples about both Ansible Roles can be found at Ansible Galaxy:
- (chilcano.apache-nifi-toolkit)[https://galaxy.ansible.com/chilcano/apache-nifi-toolkit]
- (chilcano.apache-nifi)[https://galaxy.ansible.com/chilcano/apache-nifi]

Once presented both Ansible Roles, I'm going to explain how to automate the creation of several instances of Apache NiFi, secure and not secure. See image below:

![Automated provisioning Apache NiFi multi-node cluster with Ansible and Vagrant ](https://github.com/chilcano/ansible-apache-nifi-multi-nodes/blob/master/nifi-multi-node-ansible-automation.png "Automated provisioning Apache NiFi multi-node cluster with Ansible and Vagrant")


We are going to create 5 NiFi instances, the first NiFi instance `nf1` will be a standalone instance running over HTTP.
The second instance will will be a customized instance running over HTTPS with Client Certificate authentication.
The third, fourth and fifth instances will run over HTTPS with Client Certificate authentication with configuration provided for NiFi TLS Toolkit. This configuration, key-pair, Java key stores and certificates will be generated in other VM instance provided for `chilcano.apache-nifi-toolkit Ansible Role`. See the image below:


![Apache NiFi Toolkit - folder structure and files generated](https://github.com/chilcano/ansible-apache-nifi-multi-nodes/blob/master/nifi-toolkit-files-generated.png "Apache NiFi Toolkit - folder structure and files")

I've created an Ansible Playbook for you, you can download from this Git repository: https://github.com/chilcano/ansible-apache-nifi-multi-nodes

## How to use it - steps

__1. Clone the Ansible playbooks__

```
$ git clone https://github.com/chilcano/ansible-apache-nifi-multi-nodes
```

__2. Install `chilcano.apache-nifi `and `chilcano.apache-nifi-toolkit` Ansible Roles__

```
$ cd ansible-apache-nifi-multi-nodes
$ ansible-galaxy install -r playbooks/requirements.yml
```

__3. Create all VMs with Vagrant__

Create all 6 VMs by using Vagrant.
```
$ cd infra/Vagrant
$ vagrant up
```

__4. Ansible provisioning with Vagrant__

Now, I'm going to provision (run Ansible Playbooks) through Vagrant. This step will install Apache NiFi TLS Toolkit in the `nftk1` VM, once provisioned, Vagrant will provision 5 VMs following the Ansible Playbook `playbooks/main.yml`.
It is very important to start the provision of all NiFi instances after provisioning `nftk1`.
In the `playbooks/main.yml` you will see the `nftk1` is declared on top and after `nf1, nf2, nf3, nf4` and `nf5`.

```
$ vagrant provision

$ vagrant status
Current machine states:

nftk1                     running (virtualbox)
nf5                       running (virtualbox)
nf4                       running (virtualbox)
nf3                       running (virtualbox)
nf2                       running (virtualbox)
nf1                       running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

Now we can verify if all instances are running as expected, before we have to install the  `Client Certificate` (_CN=chilcano_OU=INTIX.p12_) generated in our browser.

![Install the Client Certificate](https://github.com/chilcano/ansible-apache-nifi-multi-nodes/blob/master/nifi-multi-node-client-cert-1install.png "Install the Client Certificate")

![Select the Client Certificate](https://github.com/chilcano/ansible-apache-nifi-multi-nodes/blob/master/nifi-multi-node-client-cert-1select.png "Select the Client Certificate")

The `Client Certificate` only is required when connecting to `nf2, nf3, nf4` and `nf5` because these instances are running over HTTPS with Mutual SSL/TLS Authentication (based on Client Certificate).

![Open NiFi from Firefox](https://github.com/chilcano/ansible-apache-nifi-multi-nodes/blob/master/nifi-multi-node-browser-all.png "Open NiFi from Firefox")

## ToDo

1. Improve the Ansible Role `chilcano.apache-nifi` to implement Cluster Status coordination through `Apache ZooKeeper`.
2. Improve the Ansible Role `chilcano.apache-nifi-toolkit` to implement Client/Server mode.
3. Deploy a sample DataFlow in NiFi.

End.
