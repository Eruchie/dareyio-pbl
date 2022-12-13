**WEB SOLUTION WITH WORDPRESS - PROJECT 6**
___

In this project, we will configure a storage subsystem for web and database servers using `linux` O.S. Wordpress will be installed and connected remotely to `MySQL` database server. 

Also, this deployment will be based on a `Three-tier Architecture`, a client-server software architecture pattern that comprise of 3 separate layers:

- **Presentation Layer (PL)**: This is the user interface such as the client server or browser on your laptop.

- **Busines Layer (BL)**: This is the backend program that implements business logic, Application or Webserver.

- **Data Access or Management Layer (DAL)**: This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server.

Use `RedHat` O.S. for this project and `ec2-user` as yhe username when connecting via `ssh/putty`.

**LAUNCH AND PREPARE AN EC2 INSTANCE THAT WIL SERVE AS `WEB SERVER`**

On AWS console, create 3 volumes of 10GB each and and on the same AZ as the `web serve` and have them attached on the `web server` EC2 instance.

1. Open the `Linux` terminal and inspect what block devices are attached by running the below command:
   - `lsblk`

     ![step1](./project6Pictures/step1_p6.JPG)

1. From the above, the names of the newly created/attached devces will be likely `xvdf`, `xvdh`, `xvdg`. Note: All devices in Linux reside in `/dev/` directory. Inspect it wih the command below:

   - `ls /dev/`

     ![step2](./project6Pictures/step2_p6.JPG)

1. Enter the command below to see all mounts and free space on the `web server`.

   - `df -h`

     ![step3](./project6Pictures/step3_p6.JPG)

1. Create a single partition on each of the 3 disks using `gdisk` utility.

   - `sudo gdisk /dev/xvdf`
   - `?` (Required to print menu)
   - `p` (To print the partition table)

     ![step4](./project6Pictures/step4_p6.JPG)

1.   The following commands should be entered to create a partition.
     - `n`
     - `1`
     - `2048`
     - `20971486`
     - `8E00`
    
       ![step5](./project6Pictures/step5_p6.JPG)

1. Enter `p` to confirm/print the partition table.
     - `p`
       
       ![step6](./project6Pictures/step6_p6.JPG)

1. Write `w` table to disk and exit.
     - `w`
       
       ![step7](./project6Pictures/step7_p6.JPG)

1. Create partitions for the remaining disks and use `lsblk` utility to view the newly configured partition on each of the 3 disks.
     - `lsblk`
       
       ![step8](./project6Pictures/step8_p6.JPG)

1. Install `lvm2` package and then run `sudo lvmdiskscan` to check for available partitions.
     - `sudo yum install lvm2`

       ![step9a](./project6Pictures/step9a_p6.JPG)
     - `sudo lvmdiskscan`
       
       ![step9b](./project6Pictures/step9b_p6.JPG)

1. Mark each of 3 disks as physical volumes (PVs) to be used by LVM usint the utility `pvcreate`.
     - `sudo pvcreate /dev/xvdf1`
     - `sudo pvcreate /dev/xvdg1`
     - `sudo pvcreate /dev/xvdh1`

       ![step10](./project6Pictures/step10_p6.JPG)

1. Verify the Physical volume has been created successfully by running sudo `pvs`.
     - `sudo pvs`
     
       ![step11](./project6Pictures/step11_p6.JPG)

1. Add all 3 PVs to a volume group (VG). Name the VG `webdata-vg` using the utility `vgcreate`.
     - `sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`
     
       ![step12](./project6Pictures/step12_p6.JPG)

1. Verify that the VG has been created successfully.
     - `sudo vgs`
     
       ![step13](./project6Pictures/step13_p6.JPG)

1. Create 2 logical volumes `apps-lv`to store data for the Website and `logs-lv` used to store data for logs using `lvcreate` utility.
     - `sudo lvcreate -n apps-lv -L 14G webdata-vg`
     - `sudo lvcreate -n logs-lv -L 14G webdata-vg`
     
       ![step14](./project6Pictures/step14_p6.JPG)

1. Verify the Logical Volume has been created successfully.
     - `sudo lvs`
          
       ![step15](./project6Pictures/step15_p6.JPG)

1. Verify and view the complete setup.
     - `sudo vgdisplay -v`
          
       ![step16a](./project6Pictures/step16a_p6.JPG)

    - `sudo lsblk`

       ![step16b](./project6Pictures/step16b_p6.JPG)

1. Format the Logical Volumes with `ext4` filesystem.
     - `sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`
          
       ![step17a](./project6Pictures/step17a_p6.JPG)

    - `sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`
       
       ![step17b](./project6Pictures/step17b_p6.JPG)

1. Create `/var/www/html` directory to store website files.
     - `sudo mkdir -p /var/www/html`
          
       ![step18](./project6Pictures/step18_p6.JPG)

1. Create `/home/recovery/logs` directory to store backup of log data.
     - `sudo mkdir -p /home/recovery/logs`
          
       ![step19](./project6Pictures/step19_p6.JPG)

1. Mount `/var/www/html` on `apps-lv` Logical Volume.
     - `sudo mount /dev/webdata-vg/apps-lv /var/www/html`
          
       ![step20](./project6Pictures/step20_p6.JPG)

1. Before mounting the filesystem, use `rsync` utility to backup all the files from the log directory `/var/log` into `/home/recovery/logs`.
     - `sudo rsync -av /var/log/. /home/recovery/logs/`
          
       ![step21](./project6Pictures/step21_p6.JPG)

1. Mount `/var/log` on `logs-lv` Logical Volume. Note that all the existing data om `/var/log` will be deleted.

     - `sudo mount /dev/webdata-vg/logs-lv /var/log`
          
       ![step22](./project6Pictures/step22_p6.JPG)

1. Restore the log files back into `/var/log` directory.
     - `sudo rsync /home/recovery/logs/. /var/log`
          
       ![step23](./project6Pictures/step23_p6.JPG)

1. Update the `/etc/fstab` file so that the mount configuration will persist after restart of the server.
     - `sudo rsync /home/recovery/logs/. /var/log`
          
       ![step23](./project6Pictures/step23_p6.JPG)


