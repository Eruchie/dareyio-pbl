**DEVOPS TOOLING WEBSITE SOLUTION - PROJECT 7**
____

As a member of a DevOps team, you will implement a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible.

In this project you will implement a solution that consists of following components:

1. Infrastructure: AWS
2. Webserver Linux: Red Hat Enterprise  Linux 8
3. Database Server: Ubuntu 20.04 + MySQL
4. torage Server: Red Hat Enterprise Linux 8 + NFS Server
5. Programming Language: PHP
6. Code Repository: GitHub

On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using `Network File Sytem (NFS)` as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.

![Pic0](./project7Pictures/step0_p7.JPG)

It is important to know what storage solution is suitable for what use cases, for this – you need to answer following questions: what data will be stored, in what format, how this data will be accessed, by whom, from where, how frequently, etc. Base on this you will be able to choose the right storage system for your solution.

**STEP 1 - PREPARE NFS SERVER**
1. Setup up a new EC2 instance with RHEL Linux 8 Operating System.
2. Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB
3. Attach all three volumes one by one to your Web Server EC2 instance
4. On the NFS/Linux terminal, use `lsblk` command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. 

   - `lsblk`

     ![pic1](./project7Pictures/step1_p7.JPG)

5. Inspect it with `ls /dev/` and make sure you see all 3 newly created block devices there – their names will likely be `xvdf`, `xvdh`, `xvdg`. Also, use `df -h` command to see all mounts and free space on your server.

6. Use `gdisk` utility to create a single partition on each of the 3 disks.

   - `sudo gdisk /dev/xvdg`

     ![pic2](./project7Pictures/step2_p7.JPG)

7. Use `lsblk` utility to view the newly configured partition on each of the 3 disks.
   - `lsbk`

     ![pic3](./project7Pictures/step3_p7.JPG)

8. Install `lvm2` package using `sudo yum install lvm2` and run `sudo lvmdiskscan` command to check for available partitions.

   - `sudo yum install lvm2`
   - `sudo lvmdiskscan`

     ![pic4](./project7Pictures/step4_p7.JPG)

9. Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM.

    - `sudo pvcreate /dev/xvdf1`
    - `sudo pvcreate /dev/xvdg1`
    - `sudo pvcreate /dev/xvdh1`

      ![pic5](./project7Pictures/step5_p7.JPG)

    Verify that your Physical volume has been created successfully by running `sudo pvs`

     - `sudo pvs`

10. Use `vgcreate` utility to add all 3 PVs to a volume group (VG). Name the VG `webdata-vg`and verify that your VG has been created successfully by running `sudo vgs`.

    - `sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`
    - `sudo vgs`
  
      ![pic6](./project7Pictures/step6_p7.JPG)

11. Use `lvcreate` utility to create 3 logical volumes `lv-apps`, `lv-logs` and `lv-opt`.(Use 9GB of the PV size for each). **NOTE**: `lv-apps` and `lv-logs` will be used to store data and log files for the Website while `lv-opt` will be used for `Jenkins` in Project 8.

    - `sudo lvcreate -n lv-apps -L 9G webdata-vg`
    - `sudo lvcreate -n lv-logs -L 9G webdata-vg`
    - `sudo lvcreate -n lv-opt -L 9G webdata-vg`
  
      ![pic7](./project7Pictures/step7_p7.JPG)

12. Use `mkfs.xfs` to format the logical volumes with `xfs` filesystem.

    - `sudo mkfs -t xfs /dev/webdata-vg/lv-apps`
    - `sudo mkfs -t xfs /dev/webdata-vg/lv-logs`
    - `sudo mkfs -t xfs /dev/webdata-vg/lv-opt`
  
      ![pic8](./project7Pictures/step8_p7.JPG)

13. Navigate to `/mnt` directory and create `apps`, `logs` and `opt` folders. Also, Create mount points on `/mnt` directory for the logical volumes as follows:
- Mount `lv-apps` on `/mnt/apps` – To be used by webservers
- Mount `lv-logs` on `/mnt/logs` – To be used by webserver logs
 - Mount `lv-opt` on `/mnt/opt` – To be used by `Jenkins` server in Project 8

    - `sudo mount /dev/webdata-vg/lv-apps /mnt/apps`
    - `sudo mount /dev/webdata-vg/lv-logs /mnt/logs`
    - `sudo mount /dev/webdata-vg/lv-opt /mnt/opt`
  
      ![pic9](./project7Pictures/step9_p7.JPG)

14. Install NFS server, configure it to start on reboot and make sure it is up and running.

    - `sudo yum -y update`
    - `sudo yum install nfs-utils -y`
    - `sudo systemctl start nfs-server.service`
    - `sudo systemctl enable nfs-server.service`
    - `sudo systemctl status nfs-server.service`
  
      ![pic10](./project7Pictures/step10_p7.JPG)

15. Export the mounts for webservers’ `subnet cidr` to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security. To check your `subnet cidr` – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:

    ![pic11](./project7Pictures/step11_p7.JPG)

16. Set permission that will allow our Web servers to read, write and execute files on NFS:

    - `sudo chown -R nobody: /mnt/apps`
    - `sudo chown -R nobody: /mnt/logs`
    - `sudo chown -R nobody: /mnt/opt`

    - `sudo chmod -R 777 /mnt/apps`
    - `sudo chmod -R 777 /mnt/logs`
    - `sudo chmod -R 777 /mnt/opt`

    - `sudo systemctl restart nfs-server.service`

      ![pic12](./project7Pictures/step12_p7.JPG)

17. Navigate to `/etc` and create a file `exports` to allow NFS for clients within the same subnet (Our `Subnet CIDR – 172.31.0.0/20` ):

    - `sudo vi /etc/exports`
    - Paste the script below
   
      ```py
        /mnt/apps 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)
        /mnt/logs 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)
        /mnt/opt 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)
      ```

      ![pic13](./project7Pictures/step13_p7.JPG)

18. Run `exports -arv` and check which port is used by NFS and open it using Security Groups (add new Inbound Rule)
    - `sudo exportfs -arv`
    - `rpcinfo -p | grep nfs`

      ![pic14](./project7Pictures/step14_p7.JPG)

19. **Important note**: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049

    ![pic15](./project7Pictures/step15_p7.JPG)

   