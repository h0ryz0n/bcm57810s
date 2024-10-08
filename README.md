playing with my network board, firmwares and drivers  
this repo is intended as a collection of material found on internet with some comments I added  
I'll keep here all my outputs, drivers compiled, some src files, infos & notes  
  
hardware is a a 10Gtek® 10Gb PCI-E NIC Dual SFP+ Port board, chipset is a Broadcom BCM57810S  
  
![immagine](https://github.com/user-attachments/assets/a4325746-ef76-478f-abaf-d6eac02da406)  
  
server is a pure kvm/qemu debian hypervisor and board ports are configured in passthrough to a opnsense guest  
  
as lots of 10G boards support just 1G/10G negotiation, it needs some tinkering to unlock 2.5G in order to couple with some ONT sticks  
when fiber is not connected/you are not in O5, you could incurr in the tx fault problem = interface keeps being down at OS level, the reason is explained in links below  
  
original post: https://www.dslreports.com/forum/r32230041-Internet-Bypassing-the-HH3K-up-to-2-5Gbps-using-a-BCM57810S-NIC  
original post patch: https://www.dslreports.com/forum/r32230853-  
hack MA5671A: https://hack-gpon.org/ont-huawei-ma5671a/  
  
patch files by JAMESMTL: https://github.com/JAMESMTL/snippets/tree/master/bnx2x/patches  
debian/ubuntu procedure by JAMESMTL: https://github.com/JAMESMTL/snippets/blob/master/bnx2x/ubuntu/README-dkms.md  
  
procedure by tonusoo on github: https://github.com/tonusoo/koduinternet-cpe/blob/main/README.md  
  
opnsense: https://forum.opnsense.org/index.php?topic=13664.msg62951#msg62951  
OpnSense MA5671A no carrier issue: https://forum.opnsense.org/index.php?topic=39696.0  
  
freebsd src: https://github.com/freebsd/freebsd-src/tree/main/sys/dev/bxe  
  
freebsd & various distros - tutorial and precompiled modules: https://bxe.c-maxwell.net/en/Opnsense/modified_modules  
  
  
check MA5671A states: onu lanpsg 0 + onu ploamsg  
  
**>>> link states**
  
gpon states + some theory:  
https://forum.huawei.com/enterprise/en/gpon-introduction-common-ploam-message-using-in-gpon-system/thread/667284668879355904-667213871523442688  
https://www.draytek.co.uk/information/blog/gpon-fibre-onu-subscriber-modem-activation  
  
root@SFP:~# onu lanpsg -h  
Long Form: lan_port_status_get
Short Form: lanpsg
Input Parameter - uint32_t pport
Output Parameter
- enum onu_errorcode errorcode
- uint32_t pport
- enum lan_mode_interface mode
   LAN_MODE_OFF = 0
   LAN_MODE_GPHY = 1
   LAN_MODE_EPHY = 2
   LAN_MODE_SGMII = 3
   LAN_MODE_SGMII_FAST = 4
   LAN_MODE_RGMII_MAC = 5
   LAN_MODE_RMII_MAC = 6
   LAN_MODE_RMII_PHY = 7
   LAN_MODE_GMII_MAC = 8
   LAN_MODE_GMII_PHY = 9
   LAN_MODE_MII_MAC = 10
   LAN_MODE_MII_PHY = 11
   LAN_MODE_TMII_MAC = 12
   LAN_MODE_TMII_PHY = 13
   LAN_MODE_TBI_SERDES = 14
   LAN_MODE_TBI_AUTODETECT = 15
- uint32_t enable
- enum lan_phy_status link_status
   LAN_PHY_STATUS_OFF = 0
   LAN_PHY_STATUS_DOWN = 1
   LAN_PHY_STATUS_10_UP = 2
   LAN_PHY_STATUS_100_UP = 3
   LAN_PHY_STATUS_1000_UP = 4
   LAN_PHY_STATUS_2500_UP = 5
   LAN_PHY_STATUS_NONE = 6
   LAN_PHY_STATUS_UNKNOWN = 255
- enum lan_mode_duplex phy_duplex
   LAN_PHY_MODE_DUPLEX_AUTO = 0
   LAN_PHY_MODE_DUPLEX_FULL = 1
   LAN_PHY_MODE_DUPLEX_HALF = 2
   LAN_PHY_MODE_DUPLEX_UNKNOWN = 3
  
root@SFP:~# onu ploamsg -h  
Long Form: ploam_state_get
Short Form: ploamsg
Output Parameter
- enum onu_errorcode errorcode
- enum ploam_state curr_state
   PLOAM_STATE_O0 = 0 
   PLOAM_STATE_O1 = 1 (init) 
   PLOAM_STATE_O2 = 2 (standby/holding) 
   PLOAM_STATE_O3 = 3 (serial number) 
   PLOAM_STATE_O4 = 4 (ranging/distance measuring) 
   PLOAM_STATE_O5 = 5 (operational/running) 
   PLOAM_STATE_O6 = 6 (popup) 
   PLOAM_STATE_O7 = 7 (urgent halt) 
- enum ploam_state previous_state
   PLOAM_STATE_O0 = 0
   PLOAM_STATE_O1 = 1
   PLOAM_STATE_O2 = 2
   PLOAM_STATE_O3 = 3
   PLOAM_STATE_O4 = 4
   PLOAM_STATE_O5 = 5
   PLOAM_STATE_O6 = 6
   PLOAM_STATE_O7 = 7
- uint32_t elapsed_msec
  
  
**>>> PPPoE connection & states**
  
create vlan01  
create pppoe connection -> related to vlan  
assign interface -> pppoe  
debugging: dmesg, /var/log/messages.log, tcpdump -vvvvvv -i xxxxx  
  
https://forum.huawei.com/enterprise/en/wan-basics-pppoe/thread/667246677108604928-667213852955258880  
https://it.wikipedia.org/wiki/PPPoE  
PADI - PPPoE Active Discovery Initiation
PADO - PPPoE Active Discovery Offer
PADR - PPPoE Active Discovery Request
PADS - PPPoE Active Discovery Session-confirmation
PADT - PPPoE Active Discovery Termination 
  
****>>>some issues that may happen****  
  
- interface keeps going down if sfp not connected to fiber (tx fault):  
get a patch which disables the "tx-fault" check and recompile + check modprobe
tx_fault mask is 0=disabled, 1 or 2=enabled for single port, 3=enabled for both ports  
*in /etc/modprobe.d/txfault_patch.conf (debian-based)  
options bnx2x debug=0x4102034  
options bnx2x mask_tx_fault=3  
*in /boot/loader.conf.local (bsd based)  
#Fix states issues  
hw.bxe.interrupt_mode="1"  
net.inet.tcp.tso="0"  
#Force load module  
if_bxe_load="YES"  
#TX_FAULT MASK  
hw.bxe.mask_tx_fault=3  
hw.bxe.debug=0x00000041  

  
- interface keeps negotiating to 1000Mbps (check either sides):  
eth side  
ethtool -s enp2s0f1 speed 2500 + check with ethtool enp2s0f1  
ifconfig bxe1 media 2500Base-X mediaopt fullduplex  
sfp side  
fw_setenv sgmii_mode 5 + check with onu lanpsg 0 for link_status=5  
  
- gpon access from debian host (modded ma5671A sfp has ip 192.168.1.10 and no dhcp):  
*in /etc/network/interfaces  
auto enp2s0f1  
iface enp2s0f1 inet static  
       address 192.168.1.1/24  
       gateway 192.168.1.1  
  
- ssh to sfp stick - deprecated algorythms errror: "Unable to negotiate with 192.168.1.10 port 22: no matching key exchange method found. Their offer: diffie-hellman-group14-sha1,diffie-hellman-group1-sha1,kexguess2@matt.ucc.asn.au"  
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-dss root@192.168.1.10  
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa root@192.168.1.10
  
- OPNSense upgrade failed:  
Installing kernel-24.7.3-amd64.txz...rm: /boot/kernel.old/if_bxe.ko: Operation not permitted  
rm: /boot/kernel.old: Directory not empty  
 failed, rm error 0  
you forget to: chflags noschg /boot/kernel/if_bxe.ko  
upgrade everything, copy the modified module and put the immutable flag again  
  
  
**big thanks to original posters and all the people who contributes, they did an awesome work**  
  
