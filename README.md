openvswitch
===========

An Open vSwitch (1.4.3) package for OpenWrt release "Backfire".

Note, due to the new introduced netifd in the upcoming release "Attitude Adjustment" this package only works with the old release "Backfire".

Installation
------------

The best is to integrate this as a feed in OpenWrt

### Installation in OpenWrt

> cd $TOPDIR
> 
> echo 'src-git openvswitch git://github.com/schuza/openvswitch.git' > feeds.conf
>
> ./script/feeds/update
>
> ./script/feeds install


Please fork on githup and send pull requests.
