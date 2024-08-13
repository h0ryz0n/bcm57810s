playing with my network board, firmwares and drivers  
  
hardware is a a 10Gtek® 10Gb PCI-E NIC Dual SFP+ Port board, chipset is a Broadcom BCM57810S  
![immagine](https://github.com/user-attachments/assets/71b17a90-d41c-4950-b00a-073acba8d72a)
server is a pure kvm/qemu debian hypervisor and board ports are configured in passthrough to a opnsense guest  
as lots of 10G boards support just 1G/10G negotiation, it needs some tinkering to unlock 2.5G  
  
I'll keep here all my outputs and all the infos  
  
you can find all informations on these links, thanks to original posters and all the people who contributes  
original post: https://www.dslreports.com/forum/r32230041-Internet-Bypassing-the-HH3K-up-to-2-5Gbps-using-a-BCM57810S-NIC  
original post patch: https://www.dslreports.com/forum/r32230853-  
patch files: https://github.com/JAMESMTL/snippets/tree/master/bnx2x/patches  
opnsense: https://forum.opnsense.org/index.php?topic=13664.msg62951#msg62951  
opnsense pre-compiled: https://bxe.c-maxwell.net/en/Opnsense/modified_modules  
  
MA5671A no carrier issue: https://forum.opnsense.org/index.php?topic=39696.0  
  
freebsd src: https://github.com/freebsd/freebsd-src/tree/main/sys/dev/bxe  
