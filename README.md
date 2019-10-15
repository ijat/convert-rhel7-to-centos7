# Convert RHEL 7 to Centos 7 (October 2019)

1. Make backup of your current system and test it first in case of any error

2. Ensure curl is installed and internet connection is available to download packages

3. Remove subscription from RHEL7
```
subscription-manager remove --all
```

4. Check redhat packages installed
```
rpm -qa|egrep "rhn|redhat|subscription|cockpit"
```

5. Remove redhat packages with dependencies
```
yum remove rhnlib redhat-support-tool redhat-support-lib-python redhat-release-eula subscription-manager*
```
If you're not using cockpit (see https://cockpit-project.org/), then remove it too
```
yum remove cockpit*
```

6. Remove redhat packages without dependencies

`rpm -e --nodeps redhat-release-server`
`rm /etc/os-release.rpmsave`
```
rpm -e --nodeps redhat-rpm-config
rpm -e --nodeps redhat-logos
rpm -e --nodeps redhat-indexhtml
rpm -e --nodeps yum
```

7. Remove redhat-release folders

```
rm -rf /usr/share/doc/redhat-release/
rm -rf /usr/share/redhat-release/
```

8. Download new files from centos repo
```
mkdir tmp 
cd tmp
curl -O http://mirror.centos.org/centos/7/os/x86_64/RPM-GPG-KEY-CentOS-7
curl -O http://mirror.centos.org/centos/7/os/x86_64/Packages/yum-3.4.3-163.el7.centos.noarch.rpm
curl -O http://mirror.centos.org/centos/7/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.31-52.el7.noarch.rpm
curl -O http://mirror.centos.org/centos/7/os/x86_64/Packages/centos-release-7-7.1908.0.el7.centos.x86_64.rpm
curl -O http://mirror.centos.org/centos/7/os/x86_64/Packages/centos-logos-70.0.6-3.el7.centos.noarch.rpm
curl -O http://mirror.centos.org/centos/7/os/x86_64/Packages/centos-indexhtml-7-9.el7.centos.noarch.rpm
```

9. Install centos GPG key
```
rpm --import RPM-GPG-KEY-CentOS-7
```

10. Install new Centos packages
```
rpm -Uvh *.rpm
```

11. Update, initramfs, grub and restart
```
dracut -f
grub2-mkconfig -o /boot/grub2/grub.cfg
reboot
```

12. After reboot, clean yum and upgrade old redhat packages to Centos packages
```
yum clean all
yum upgrade
```
