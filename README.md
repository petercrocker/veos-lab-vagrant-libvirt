A Packer template for creating an Arista vEOS-lab Vagrant box for the [libvirt](https://libvirt.org) provider.

## Fork Info

Forked from https://github.com/mweisel/veos-lab-vagrant-libvirt

Modified file arista-veos-lab.json to create a vEOS image with interface Ethernet1 set as DHCP, with a static MAC address of 44:38:39:00:00:56 for use in the this [Cumulus Linux EVPN interop vagrant environment](https://gitlab.com/petercrocker/cl-evpn-arista-interop.git). 

## Prerequisites

  * [Arista](https://www.arista.com/en/user-registration) account
  * [Git](https://git-scm.com)
  * [Packer](https://packer.io)
  * [libvirt](https://libvirt.org)
  * [QEMU](https://www.qemu.org)
  * [Vagrant](https://vagrantup.com)
  * [vagrant-libvirt](https://github.com/vagrant-libvirt/vagrant-libvirt)

## Steps

1. Clone this GitHub repo and _cd_ into the directory.

```
git clone https://github.com/mweisel/veos-lab-vagrant-libvirt
cd veos-lab-vagrant-libvirt
```

2. Log in and download the vEOS-lab disk image from [Arista](https://www.arista.com/support/software-download).

<img src="https://res.cloudinary.com/binarynature/image/upload/v1573883854/veos-download-from-arista-site_vdzstv.png" width="410" height="369">

3. Convert the vEOS-lab disk image from `vmdk` to `qcow2`.

```
qemu-img convert -pO qcow2 ~/Downloads/vEOS-lab-4.23.1F.vmdk ~/Downloads/vEOS-cumulus.qcow2
qemu-img check ~/Downloads/vEOS-cumulus.qcow2
qemu-img info ~/Downloads/vEOS-cumulus.qcow2
```

4. Copy the converted vEOS-lab disk image to the `/var/lib/libvirt/images` directory.

```
sudo cp ~/Downloads/vEOS-cumulus.qcow2 /var/lib/libvirt/images
```

5. Modify the file ownership and permissions. Note the owner will differ between Linux distributions. A couple of examples:

> Arch Linux
```
sudo chown nobody:kvm /var/lib/libvirt/images/vEOS-cumulus.qcow2
sudo chmod u+x /var/lib/libvirt/images/vEOS-cumulus.qcow2
```

> Ubuntu 18.04
```
sudo chown libvirt-qemu:kvm /var/lib/libvirt/images/vEOS-cumulus.qcow2
sudo chmod u+x /var/lib/libvirt/images/vEOS-cumulus.qcow2
```

6. Packer _build_ with the vEOS version as a variable to create the Vagrant box artifact.

```
packer build -var 'version=4.23.1F' arista-veos-cumulus.json
```

7. Add the Vagrant box. 

```
vagrant box add --provider libvirt --name arista/veos-cumulus-4.23.1F ./builds/veos-cumulus-4.23.1F-libvirt.box
```

8. Vagrant Up!

[![asciicast](https://asciinema.org/a/283821.svg)](https://asciinema.org/a/283821?speed=4)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
