# Asustor: Creating startup script

1. Connect by SSH
2. sudo to root
3. Create `scripts` directory: `/volume1/.@root/scripts`
4. Create the script:
   ```shell
   cat nas_start_shutdown_script.sh
   #!/bin/sh

   echo "NAS Startup/Shutdown script"

   startup_script()
   {
       #sleep 60 && /opt/VirtualBox/VBoxManage startvm 2a1036d8-bf83-4090-b0ed-7122ceafb917 --type headless
       echo "Mounting needed filesystems..."
       mount -t nfs 192.168.10.253:/volume2/Backups /volume3/Backups
       mount -t nfs 192.168.10.253:/volume2/Backups /share/Backups
   }

   shutdown_script()
   {
       echo "Unmounting filesystems..."
       umount /volume3/Backups
   }

   install()
   {
       ln -s /volume1/.@root/scripts/nas_start_shutdown_script.sh /usr/local/etc/init.d/S99autostart
       ln -s /volume1/.@root/scripts/nas_start_shutdown_script.sh /usr/local/etc/init.d/K01autostart
   }
   case "$1" in
       start)
           echo "Executing startup script..."
   	startup_script
           sleep 3
           ;;
       stop)
           echo "Executing shutdown script..."
   	shutdown_script
           sleep 3
           ;;
       reload)
   	shutdown_script
           sleep 2
   	startup_script
           ;;
       install)
   	install
   	;;
       *)
           echo "Usage: $0 {start|stop|reload|install}"
           exit 1
           ;;
   esac
   exit 0
   ```
5. Execute: `/volume1/.@root/scripts/nas_start_shutdown_script.sh install`
6. It will create:
   ```shell
   ls -latr /usr/local/etc/init.d/
   total 84
   drwxr-xr-x    1 root     root            24 Mar 12 21:39 ../
   lrwxrwxrwx    1 root     root            52 Mar 26 11:30 S99autostart -> /volume1/.@root/scripts/nas_start_shutdown_script.sh*
   lrwxrwxrwx    1 root     root            52 Mar 26 11:30 K01autostart -> /volume1/.@root/scripts/nas_start_shutdown_script.sh*
   ```
