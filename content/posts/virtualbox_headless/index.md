+++
title = "Virtualbox, oui mais sans (prise de) tête !"
date = "2023-10-31T20:58:36+0000"
authors = ["kn"]
cover = ""
tags = ["linux", "virtualbox"]
keywords = ["virtualbox", "headless"]
description = "headless virtual machine"
showFullContent = false
+++

It may be interesting to have a Linux machine within range of SSH that is always available and that is windowless, i.e. running in headless mode .

For example, under MacOS, you can have Linux tools without being cluttered with a window associated with this Linux.

The advantage is to run a VM in the background without using a lot of memory.

There are two distinct parts to master:

> start and stop the VM in command line mode;
>
> allow connection by SSH from the host to this VM.
To know the name of the VM:

```bash
VBoxManage list vms
```

To launch the vm, here called *myvm*:

```bash
VBoxHeadless --startvm "myvm"
```

Then, we can do *Port Forwarding* to reach the VM from the host:

```bash
VBoxManage controlvm myvm natpf1 vers_ssh,tcp,,2224,,22
```

Finally, if you want to properly shut down the VM:

```bash
VBoxManage controlvm "myvm" acpipowerbutton
```

If we want to ensure access to the VM is possible:

```bash
VBoxManage showvminfo "myvm"
NIC 1 Rule(0):   name = vers_ssh, protocol = tcp, host ip = , host port = 2224, guest ip = , guest port = 22
```

To connect via SSH:

```bash
ssh -p 2224 localhost
```



