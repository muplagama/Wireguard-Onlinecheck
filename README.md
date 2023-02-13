# Wireguard-Onlinecheck
WG Onlinecheck


nano /bin/killswitch

```
#!/bin/bash
while true
do
count=$( ping -c 3 -I wg0 1.1.1.1 | grep time | grep ttl | wc -l )
    if [ $count -eq 0 ];
     then
        _date=$(date +"%F %H:%M:%S")
echo "wg0 offline"
  echo -e "$_date wg0 offline" >>  /tmp/ipinfo.log
  wg-quick down wg0
  wg-quick up wg0
  fi
done
```
chmod +x /bin/killswitch

nano /etc/init.d/killswitch

```
#!/bin/sh
NAME=killswitch
VNAME="WG Pingcheck"
VINFO="Enigma2 / Wireguard Pingcheck"

case "$1" in
    start)
        killswitch 2>&1 &
        ;;
     stop)
        pkill killswitch
        ;;
     enable)
        echo "enable autostart"
        update-rc.d $NAME defaults > /dev/null 2>&1
        /etc/init.d/$NAME start &
        ;;
     disable)
        /etc/init.d/killswitch stop
        echo "remove autostart"
        update-rc.d -f $NAME remove > /dev/null 2>&1
        ;;
     version)
        echo "$VNAME"
        ;;
     info)
        echo "$VINFO"
        ;;
    *)
        echo "usage: $0 start|stop|enable|disable|version|info" >&2
       exit 1
       ;;
esac
exit


chmod +x /etc/init.d/killswitch

/etc/init.d/killswitch enable
