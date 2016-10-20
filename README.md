### requirements:

* Vagrant
* Ansible 2.0+
* jdk-8u66-linux-x64.tar.gz

### get start

```
git clone https://git.oschina.net/oscplatform/kafka-lab.git
## put jdk-8u66-linux-x64.tar.gz into kafka/ansible/jdk8/files folder
cd kafka-lab
vagrant up
ansible-playbook ./ansible/playbook.yml -i ./ansible/inventory -u vagrant -k
```
