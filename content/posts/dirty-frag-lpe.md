---
title: 'Dirty Frag: Linux Kernel LPE'
date: 2026-05-09
draft: false
---

dirty frag is a new linux lpe that chains memory fragmentation with page cache corruption. affects 5.8-6.8 kernels. no special privs needed.

the exploit fills memory with junk in a specific pattern, then triggers page cache to align kernel objects where it wants them. sandbox boundary gets crossed. userland to kernel from inside a container.

most major distros have not patched yet. ubuntu, fedora, debian all vulnerable in current stable releases. patches exist but adoption is slow.

doesnt require kernel modules or existing container access. just runs from userland. kind of defeats the whole point of the sandbox.

if your infrastructure is on unpatched 5.x or 6.x kernels you should probably know about this.
