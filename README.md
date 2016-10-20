### requirements:

* Vagrant
* Ansible 2.0+

### get start

```
git clone https://git.oschina.net/oscplatform/kafka-lab.git
cd kafka-lab
vagrant up
ansible-playbook ./ansible/playbook.yml -i ./ansible/inventory -u vagrant -k
```
