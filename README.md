# Kubevirt Demo

This demo will set up two vagrant VMs. One is a kubernetes master with
kubernetes and kubevirt deployed, the other one is a kubernetes node. This
setup allows you to do live migrations of VMs.

## Getting it up and running

### Fedora

```bash
# Install vagrant and libvirt
sudo dnf install vagrant vagrant-libvirt virt-viewer libvirt-client
sudo systemctl restart virtlogd # Work around rpm packaging bug
sudo systemctl restart libvirtd

# Check out the repo
git clone git@github.com:rmohr/kubevirt-vagrant-demo.git
cd kubevirt-vagrant-demo

# start the environment
# it will take some time until all pods are ready
vagrant up

# fetch the kubernetes connection details from the master VM
cluster/kubectl.sh --init
```

## Playing with VMs

Now you can create a VM and wait until it is running:

```bash
cluster/kubectl.sh create -f cluster/vm.yaml
watch cluster/kubectl.sh get vms -o=custom-columns=NAME:metadata.name,PHASE:status.phase,NODE:status.nodeName
```

When the VM is running, the VM can be migrated:

```bash
cluster/kubectl.sh create -f cluster/migration.yaml
watch cluster/kubectl.sh get vms -o=custom-columns=NAME:metadata.name,PHASE:status.phase,NODE:status.nodeName
```

There is also direct access to libvirt inside the VM to verify that the VM really moved:

```bash
vagrant ssh master -c 'sudo virsh list'
vagrant ssh node0 -c 'sudo virsh list'
```

Finally, a SPICE connection can be opened to the VMs:

```bash
cluster/kubectl.sh spice testvm
```
