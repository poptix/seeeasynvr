# seeeasynvr

I purchased a couple of these in December 2023 because the price was right and I just needed a device capable of reliably displaying an RTSP stream on a TV.

## console

A serial console (115200 8n1) is available on the center two pins of J3, +3.3v on the SQUARE pin, ground is the remaining pin. You do not need to apply voltage, leave the power disconnected and power the board via the 12v plug. 

This system uses uBoot and is interruptible (it says "any key" but only ^C seems to work for me). 

```
isvp_a1# printenv
baudrate=115200
bootargs=console=ttyS1,115200n8 asc_sn=0002fb9b85a15300000000000000000000000003ec00000000 flash_type=0 init=/linuxrc rootfstype=squashfs root=/dev/mtdblock3 ro board_type=0x3ec local_av=1 have_face=0 system_mode=0 mtdparts=jz_sfc:192K(uboot),64K(env),1856K(kernel_m2),12288K(rootfs_m3),1920K(config_m4),64K(logo_m5) licence=1.5 libata.force=1.5Gbps mem=190M@0x0 rmem=66M@0xbe00000
bootcmd=sf probe 0x0; sf read 0x80600000 0x40000 0x1d0000;bootm 0x80600000;
bootdelay=1
eth0mii=rmii
eth1addr=00:11:22:56:96:70
eth1mii=rgmii
ethact=mii0
ethaddr=00:53:a1:85:9b:fb
ethprime=mii0
ethrotate=no
gatewayip=193.169.4.1
ipaddr=193.169.4.151
jpeg_addr=0x81000000
jpeg_size=0x10000
loads_echo=1
netmask=255.255.255.0
serverip=193.169.4.2
stderr=serial
stdin=serial
stdout=serial
system_mode=0
vobuf=0x8f600000
```

## init 

bootargs specifies init=/linuxrc, this is standard busybox init. 

/linuxrc -> /etc/init.d/rcS -> /etc/init.d/S99ants 

## S99ants

This script mounts the various mtd devices, populates /var/ from /etc/soft_link/, then runs /mnt/mtd/app/init_board.sh

init_board.sh performs a lot of housekeeping, and eventually invokes the nvr software, the GUI, an update manager, etc. 

## root

root:CYjoTKVIDJ0bg:0:0::/root:/bin/sh

Standard DES crypt, the password is "tianwang". You can login as root on the serial console. 

## Observations

It tries pretty hard to phone home, even before system setup. 

Configured/DHCP DNS is ignored, it directly probes various public nameservers (8.8.8.8, 223.5.5.5, 223.6.6.6) 

Attempts to connect to an MQTT server (public.iot-as-mqtt.cn-shanghai.aliyuncs.com)

After initial configuration and disabling of P2P (app) and NTP it appears to stop attempting outbound connections. 

## Directory listings

### /mnt/mtd/app
```
ants_diagnose                  liblive.so
ants_gui                       liblog_manage.so
ants_system                    libmodel.so
audio                          libnetwork.so
consoleToTelnet.sh             libonvif_mxml.so
ddns                           libonvif_server.so
default_config                 libplatform.so
flash_cache.sh                 libplayback.so
gui_custom_company_config.xml  libqrencode.so
host                           librecfile_rw.so
impdbg                         librecord.so
init_board.sh                  libremote_agent.so
insmod_nfs.sh                  librtmp_proxy.so
ivs_lib                        librtsp_proxy.so
libaccess_manage.so            libsmart.so
libarp_tool.so                 libsqlite3.so
libatasmart.so                 libsqlitecpp.so
libbackup.so                   libss.so.2
libblkid.so.1                  libupgrade.so
libcfg_manage.so               libuser_manage.so
libcom_err.so.2                libutility_plat.so
libdiscovery.so                libuuid.so.1
libdisk_manage.so              libws_proxy.so
libe2p.so.2                    logcat
libext2fs.so.2                 mpp
libfdisk.so.1                  noauthority.nvr
libgb28181.so                  protocal_lib
libhandle.so.1                 serial
libhot_spare.so                soft_version.xml
libhttp_proxy.so               telnetToConsole.sh
libhttp_server.so              tool
```

### /config 
```
aliIoT4nvr                      ants_object_config.xml
ants_absent_config.xml          ants_od_config.xml
ants_ai_config.xml              ants_parking_config.xml
ants_ao_config.xml              ants_ptz_soft_ctrl_cfg.xml
ants_channel_device_config.xml  ants_record_config.xml
ants_counter_config.xml         ants_region_config.xml
ants_electric_config.xml        ants_retrograde_config.xml
ants_exp_config.xml             ants_staying_config.xml
ants_gui_config.xml             ants_system_config.xml
ants_gui_priview_config.xml     ants_usr_config.xml
ants_hd_config.xml              ants_vlost_config.xml
ants_humanoid_config.xml        ants_wire_config.xml
ants_invasion_config.xml        custom
ants_iptables_rule_cfg.xml      hostmgr_autoupgrade
ants_md_config.xml              reserve
ants_network_config.xml         upgrade_restore_factory
```




