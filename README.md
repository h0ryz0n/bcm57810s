playing with my network board, firmwares and drivers  
I'll keep here all my outputs, drivers compiled, some src files, infos & notes
  
hardware is a a 10Gtek® 10Gb PCI-E NIC Dual SFP+ Port board, chipset is a Broadcom BCM57810S  
![immagine](https://github.com/user-attachments/assets/a4325746-ef76-478f-abaf-d6eac02da406)  
server is a pure kvm/qemu debian hypervisor and board ports are configured in passthrough to a opnsense guest

as lots of 10G boards support just 1G/10G negotiation, it needs some tinkering to unlock 2.5G in order to couple with some ONT sticks    

original post: https://www.dslreports.com/forum/r32230041-Internet-Bypassing-the-HH3K-up-to-2-5Gbps-using-a-BCM57810S-NIC  
original post patch: https://www.dslreports.com/forum/r32230853-  

patch files by JAMESMTL: https://github.com/JAMESMTL/snippets/tree/master/bnx2x/patches  
debian/ubuntu procedure by JAMESMTL: https://github.com/JAMESMTL/snippets/blob/master/bnx2x/ubuntu/README-dkms.md
  
procedure by tonusoo on github: https://github.com/tonusoo/koduinternet-cpe/blob/main/README.md  
  
opnsense: https://forum.opnsense.org/index.php?topic=13664.msg62951#msg62951  
opnsense pre-compiled: https://bxe.c-maxwell.net/en/Opnsense/modified_modules  
OpnSense MA5671A no carrier issue: https://forum.opnsense.org/index.php?topic=39696.0  

freebsd src: https://github.com/freebsd/freebsd-src/tree/main/sys/dev/bxe  

issues i had:  
- interface keeps going down if sfp not connected to fiber (tx fault):  
need the "no tx-fault" patch and recompile +   
*in /etc/modprobe.d/txfault_patch.conf  
options bnx2x debug=0x4102034  
options bnx2x mask_tx_fault=3  [or 1, 2 for single port]  
  
- interface keeps negotiating to 1000Mbps (check either sides):  
(eth side) ethtool -s <if> speed 2500 + check with ethtool <if>  
(sfp side) fw_setenv sgmii_mode 5 + check with onu lanpsg 0 for link_status=5  

- gpon access from debian host (modded ma5671A sfp has ip 192.168.1.10 and no dhcp):
*in /etc/network/interfaces  
auto enp2s0f1  
iface enp2s0f1 inet static  
       address 192.168.1.1/24  
       gateway 192.168.1.1  


  
thanks to original posters and all the people who contributes, they did an awesome work
