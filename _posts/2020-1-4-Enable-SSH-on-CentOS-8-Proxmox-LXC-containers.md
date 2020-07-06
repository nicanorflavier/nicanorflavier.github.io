---
layout: post
title: Enable SSH on CentOS 8 Proxmox LXC containers
permalink: /blog/:title
comments: true
---
The CentOS 8 container template that comes by default in Proxmox does not have the SSH enabled and installed. That means it will not be convenient if you deploy hundreds of containers to manage it, as you need to go to the Proxmox host console first before managing the containers. The easier way is for systems administrators to use remote desktop connection manager tools like (mRemoteNG or PAC Manager) to connections. That means you have to enable SSH in each one of the containers. To enable the SSH on CentOS 8 container, you have to do the following.

1. Login as root and update the latest packages

    ```bash
    dnf update
    ```

2. Install SSH server and Vim (a better CLI text editor than Vi) using the new package manager DNF (Dandified YUM)
    ```bash
    dnf install vim openssh-server
    ```

    <div>Output:</div>
    ```bash
      nicky@unknownserver ~]# dnf install vim openssh-server
      Last metadata expiration check: 0:58:27 ago on Thu Jan 2 19:21:30 2020.
      Dependencies resolved.
      ===========================================================================================================================
      Package                        Architecture           Version                             Repository                 Size
      ===========================================================================================================================
      Installing:
      openssh-server                 x86_64                 8.0p1-4.el8_1                       BaseOS                    485 k
      vim-enhanced                   x86_64                 2:8.0.1763-13.el8                   AppStream                 1.4 M
      Installing dependencies:
      gpm-libs                       x86_64                 1.20.7-15.el8                       AppStream                  39 k
      vim-common                     x86_64                 2:8.0.1763-13.el8                   AppStream                 6.3 M
      vim-filesystem                 noarch                 2:8.0.1763-13.el8                   AppStream                  48 k

      Transaction Summary
      ===========================================================================================================================
      Install  5 Packages

      Total download size: 8.3 M
      Installed size: 32 M
      Is this ok [y/N]: y
      Downloading Packages:
      (1/5): gpm-libs-1.20.7-15.el8.x86_64.rpm                                                   124 kB/s |  39 kB     00:00    
      (2/5): vim-filesystem-8.0.1763-13.el8.noarch.rpm                                           1.6 MB/s |  48 kB     00:00    
      (3/5): openssh-server-8.0p1-4.el8_1.x86_64.rpm                                             1.3 MB/s | 485 kB     00:00    
      (4/5): vim-enhanced-8.0.1763-13.el8.x86_64.rpm                                             1.8 MB/s | 1.4 MB     00:00    
      (5/5): vim-common-8.0.1763-13.el8.x86_64.rpm                                               4.2 MB/s | 6.3 MB     00:01    
      ---------------------------------------------------------------------------------------------------------------------------
      Total                                                                                      2.8 MB/s | 8.3 MB     00:02     
      Running transaction check
      Transaction check succeeded.
      Running transaction test
      Transaction test succeeded.
      Running transaction
        Preparing        :                                                                                                   1/1 
        Installing       : vim-filesystem-2:8.0.1763-13.el8.noarch                                                           1/5 
        Installing       : vim-common-2:8.0.1763-13.el8.x86_64                                                               2/5 
        Installing       : gpm-libs-1.20.7-15.el8.x86_64                                                                     3/5 
        Running scriptlet: gpm-libs-1.20.7-15.el8.x86_64                                                                     3/5 
        Installing       : vim-enhanced-2:8.0.1763-13.el8.x86_64                                                             4/5 
        Running scriptlet: openssh-server-8.0p1-4.el8_1.x86_64                                                               5/5 
        Installing       : openssh-server-8.0p1-4.el8_1.x86_64                                                               5/5 
        Running scriptlet: openssh-server-8.0p1-4.el8_1.x86_64                                                               5/5 
        Running scriptlet: vim-common-2:8.0.1763-13.el8.x86_64                                                               5/5 
        Verifying        : gpm-libs-1.20.7-15.el8.x86_64                                                                     1/5 
        Verifying        : vim-common-2:8.0.1763-13.el8.x86_64                                                               2/5 
        Verifying        : vim-enhanced-2:8.0.1763-13.el8.x86_64                                                             3/5 
        Verifying        : vim-filesystem-2:8.0.1763-13.el8.noarch                                                           4/5 
        Verifying        : openssh-server-8.0p1-4.el8_1.x86_64                                                               5/5 

      Installed:
        gpm-libs-1.20.7-15.el8.x86_64           openssh-server-8.0p1-4.el8_1.x86_64       vim-common-2:8.0.1763-13.el8.x86_64  
        vim-enhanced-2:8.0.1763-13.el8.x86_64   vim-filesystem-2:8.0.1763-13.el8.noarch  

      Complete!
    ```

{:start="3"}
3. Start and enable SSH when computer starts
    ```bash
    systemctl start sshd
    systemctl enable sshd
    ```
4. For security purposes it is not recommended to SSH as root, we will create another user named brad and use that user instead to SSH and do administrator tasks at the same time, in this case we will enable sudo. The sudo command allows users to run programs with the security privileges of another user, by default the root user. That means we will never ever use root login, except for emergency purposes. I highly recommend to do it this way for security purposes. First we will create another user.

    ```bash
    useradd brad
    ```

5. Set the password, for security purposes always use a good password combination, as this will have super user privilege.

    ```bash
    passwd brad
    ```
6. Install sudo package, as sometimes not included by default in CentOS 8.
    ```bash
    dnf install sudo
    ```
7. Add user to the wheel group, all members in the wheel group have sudo access. Means they are super users without being root.
    ```bash
    usermod -aG wheel brad
    ```
8. Test your sudo privilege if it is working fine, switch as user brad.
    ```bash
    su - brad
    ```
9. Now we are logged in as brad, lets try our super user privilege by listing the root directory, this is normally only accessible to the root user.
    ```bash
    sudo ls -la /root
    ```
    <div>Output:</div>
    ```bash
    [brad@unknownserver ~]$ sudo ls -la /root
      total 36
      dr-xr-x---  2 root root 4096 Jan  2 19:10 .
      drwxr-xr-x 19 root root 4096 Jan  2 19:07 ..
      -rw-------  1 root root  942 Jan  2 19:10 .bash_history
      -rw-r--r--  1 root root   18 May 11  2019 .bash_logout
      -rw-r--r--  1 root root  176 May 11  2019 .bash_profile
      -rw-r--r--  1 root root  176 May 11  2019 .bashrc
      -rw-r--r--  1 root root  100 May 11  2019 .cshrc
      -rw-r--r--  1 root root  129 May 11  2019 .tcshrc
      -rw-------  1 root root  532 Jan  2 19:10 .viminfo
    ```
10. Now we will disable SSH root login using vim text editor, this time we will use again the sudo command.
   ```bash 
   sudo vim /etc/ssh/sshd_config
   ```
11. We have to change the parameter to yes, this will enable that parameter to disable the root login in SSH. Make sure to save the file after making the changes and then exit.

    <div>From:</div>
    ```bash 
    PermitRootLogin yes
    ```
    <div>To:</div>
    ```bash 
    PermitRootLogin no
    ```
12. Now restart SSH daemon service.
    ```bash
    sudo systemctl restart sshd
    ```
13. Now you can try SSH as root and you should get the error message.

    <div class="alert alert-danger" role="alert">Access denied </div>

14. Do not forget to synchronize our time, since we are running an unprivilege container, we are unable to use NTP or chrony daemon on the container, however we can change the current timezone, we can manually do this by using the timedatectl command. The command below will show you your current timezone.
    ```bash
    timedatectl
    ```
    <div>Output:</div>
    ```bash
    Local time: Thu 2020-01-02 10:20:58 UTC
    Universal time: Thu 2020-01-02 10:20:58 UTC
    RTC time: n/a
    Time zone: UTC (UTC, +0000)
    System clock synchronized: yes
    NTP service: inactive
    RTC in local TZ: no
    ```
15. List the available time zones.
    ```bash
    timedatectl list-timezones
    ```
16. Set the timezone to Australia/Adelaide which is my current timezone here, select the one appropriate for your timezone.
    ```bash
    sudo timedatectl set-timezone Australia/Adelaide
    ```
<h2>Conclusion</h2>
<div class="alert alert-dark" role="alert">
We have enabled SSH, enabled sudo users and have disabled SSH root login, now you can create this as a template in your Proxmox so everytime you will provision new containers you do not need to redo the whole steps. This guide is applicable anywhere, whether you are using VMs or if you are doing a VM in a cloud like AWS or Azure, you can follow the steps as well.
</div>

<div class="alert alert-info" role="alert">
If you like the article, please comment and or share the article, remember sharing is caring.
</div>
