# Kubernetes on Fedora Vagrant Lab
Automatically deploy a local k8s cluster on Fedora VMs with Vagrant and Ansible

Uses [Jeff Geerling's k8s Ansible role](https://github.com/geerlingguy/ansible-role-kubernetes)

## Requirements
- [Vagrant](https://www.vagrantup.com/)
- [Vagrant libvirt provider](https://github.com/vagrant-libvirt/vagrant-libvirt)
- [Libvirt, QEMU/KVM etc.](https://wiki.libvirt.org/page/Main_Page)
- Possibly more...

## How do I use it?
In theory:

`git clone https://github.com/bcrisp4/k8s-vagrant-lab.git && cd k8s-vagrant-lab`
`vagrant up`

## Caveats
- Written to use libvirt provider, not the default VirtualBox. Why? Personal preference. Would probably work with Virtual box with limited effort
- Only tested on Fedora 30, your milage on other OSs may vary
- May require some manual network reconfiguration, especially for bridged network interfaces (if you need that)

## Helpful links
- [Fedora 22 libvirt with bridge](https://lukas.zapletalovi.com/2015/09/fedora-22-libvirt-with-bridge.html)
- [Libvirt networking](https://wiki.libvirt.org/page/Networking)
- [Configuring QEMU bridge helper after “access denied by acl file” error](https://blog.christophersmart.com/2016/08/31/configuring-qemu-bridge-helper-after-access-denied-by-acl-file-error/)

## License
MIT / BSD

