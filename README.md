openvswitch
===========

UPDATE: Works now with OpenWrt "Barrier Breaker" (Bleeding Edge)

Open vSwitch 1.9.0 (OvS) package for OpenWrt

Installation
------------

Install this as a feed!

### Installation in OpenWrt

> cd $TOPDIR
> 
> echo 'src-git openvswitch git://github.com/schuza/openvswitch.git' >> feeds.conf
>
> ./scripts/feeds update openvswitch
>
> ./scripts/feeds install -a -p openvswitch
> 
> make menuconfig
>
> select Network -> openvswitch-switch, openvswitch-brcompat and openvswitch-controller
>
> echo '# CONFIG_KERNEL_BRIDGE is not set' >> .config



Development
-----------

Please fork on githup and send pull requests.
