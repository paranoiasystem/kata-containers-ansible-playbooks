# Kata Containers Ansible Installation Playbooks

## Overview
This repository contains Ansible playbooks for installing Kata Containers in environments that use containerd and Kubernetes. Kata Containers provide a lightweight, secure container runtime with a footprint similar to traditional containers, but with the security advantages of virtual machines.

## Prerequisites
Ensure you have Ansible installed on your system. Additionally, you should have access to a Kubernetes cluster where Kata Containers will be deployed.

## Installation

### Installing Kata Containers
To install Kata Containers on your containerd nodes, navigate to the `ansible` directory and run the playbook using the following commands:

```bash
cd ansible
ansible-playbook install-kata-containers.yml
```

This playbook will configure your containerd setup to use Kata Containers as a runtime option.

### Testing on Kubernetes
To test the installation on a Kubernetes cluster, apply the provided Kubernetes manifests:

```bash
cd k8s
kustomize build | kubectl apply -f -
```

This will deploy a test pod using Kata Containers runtime.

## Post-Installation Verification

### Verifying the Pod's Node
To check the worker node where the Kata Containers pod is running, use the following command:

```bash
kubectl get pods -n default kata-busybox -o jsonpath='{.spec.nodeName}'
```

### Checking QEMU Processes
Connect to the node where the pod is running and search for QEMU processes, which indicate that Kata Containers are running:

```bash
ps aux | grep qemu
```

You should see output similar to the following, showing the QEMU process associated with the running Kata Containers:

```bash
# Sample output from `ps aux | grep qemu`
root        3219  0.0  0.0   6448  1240 ?        Ss   09:40   0:00 /usr/sbin/qemu-ga
root       37035  0.4  1.8 2670456 153248 ?      Sl   10:00   0:01 /opt/kata/bin/qemu-system-x86_64 -name sandbox-57c1765d4751c5104cbf82aaef4e8ad646855a5558d50fbaa241f6863d760f53 -uuid 3ceb96d1-39af-4603-a941-2285ad64ede5 -machine q35,accel=kvm,nvdimm=on -cpu host,pmu=off -qmp unix:fd=3,server=on,wait=off -m 2048M,slots=10,maxmem=8969M -device pci-bridge,bus=pcie.0,id=pci-bridge-0,chassis_nr=1,shpc=off,addr=2,io-reserve=4k,mem-reserve=1m,pref64-reserve=1m -device virtio-serial-pci,disable-modern=true,id=serial0 -device virtconsole,chardev=charconsole0,id=console0 -chardev socket,id=charconsole0,path=/run/vc/vm/57c1765d4751c5104cbf82aaef4e8ad646855a5558d50fbaa241f6863d760f53/console.sock,server=on,wait=off -device nvdimm,id=nv0,memdev=mem0,unarmed=on -object memory-backend-file,id=mem0,mem-path=/opt/kata/share/kata-containers/kata-ubuntu-latest.image,size=268435456,readonly=on -device virtio-scsi-pci,id=scsi0,disable-modern=true -object rng-random,id=rng0,filename=/dev/urandom -device virtio-rng-pci,rng=rng0 -device vhost-vsock-pci,disable-modern=true,vhostfd=4,id=vsock-2889501141,guest-cid=2889501141 -chardev socket,id=char-78547e18896919db,path=/run/vc/vm/57c1765d4751c5104cbf82aaef4e8ad646855a5558d50fbaa241f6863d760f53/vhost-fs.sock -device vhost-user-fs-pci,chardev=char-78547e18896919db,tag=kataShared,queue-size=1024 -netdev tap,id=network-0,vhost=on,vhostfds=5,fds=6 -device driver=virtio-net-pci,netdev=network-0,mac=5a:20:c0:26:98:97,disable-modern=true,mq=on,vectors=4 -rtc base=utc,driftfix=slew,clock=host -global kvm-pit.lost_tick_policy=discard -vga none -no-user-config -nodefaults -nographic --no-reboot -object memory-backend-file,id=dimm1,size=2048M,mem-path=/dev/shm,share=on -numa node,memdev=dimm1 -kernel /opt/kata/share/kata-containers/vmlinux-6.1.38-114 -append tsc=reliable no_timer_check rcupdate.rcu_expedited=1 i8042.direct=1 i8042.dumbkbd=1 i8042.nopnp=1 i8042.noaux=1 noreplace-smp reboot=k cryptomgr.notests net.ifnames=0 pci=lastbus=0 root=/dev/pmem0p1 rootflags=dax,data=ordered,errors=remount-ro ro rootfstype=ext4 console=hvc0 console=hvc1 quiet systemd.show_status=false panic=1 nr_cpus=4 selinux=0 systemd.unit=kata-containers.target systemd.mask=systemd-networkd.service systemd.mask=systemd-networkd.socket scsi_mod.scan=none -pidfile /run/vc/vm/57c1765d4751c5104cbf82aaef4e8ad646855a5558d50fbaa241f6863d760f53/pid -smp 1,cores=1,threads=1,sockets=4,maxcpus=4
ubuntu     40480  0.0  0.0   8160  2480 pts/0    S+   10:08   0:00 grep --color=auto qemu
```

## Support and Contributions
For support or to contribute to this project, please open an issue or a pull request in this repository.

## License
This project is licensed under the MIT License. For more details, please see the [LICENSE](./LICENSE) file.