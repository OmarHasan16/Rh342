# RH342 Study Sheet

This is a short list of information to assist in review prior to the exam. This is not a comprehensive list of objectives or commands.

## System Boot

### Regain root control
To regain root access, break into the boot process and reset the root password:
```bash
rd.break within Grub
boot system
mount -oremount,rw /sysroot
chroot /sysroot
passwd
load_policy -i && restorecon -Rv /etc
```

### Reinstall Grub Config (BIOS)
To regenerate the GRUB configuration file:
```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

## Performance Co-Pilot

### List metrics with terse descriptions
```bash
pminfo -t
```

### Query metric
```bash
pmval <metric>
```

### Query for time interval
```bash
pmval -T 5m <metric>
```

### Query archive interval values
```bash
pmval -t 5m <metric> -a <file>
```

## Remote Logging

### Config File
The primary configuration file for rsyslog:
```bash
/etc/rsyslog.conf
```

## Package Management

### Query file for package
```bash
rpm -qf <file>
```

### List content of package
```bash
rpm -qlp <package>
```

### List package dependencies
```bash
rpm -qR <package>
# OR
yum deplist <package>
```

### Find package that provides a file
```bash
yum provides */<file>
```

### Exclude a package
Add the following to `/etc/yum.conf` to prevent installation:
```bash
exclude=<package>
```

### Version locking
Lock a package version to prevent upgrades:
```bash
yum install -y yum-plugin-versionlock
yum versionlock <package>
```

### Verify RPM database integrity
```bash
/usr/lib/rpm/rpmdb_verify /var/lib/rpm/Packages
```

### Create a new RPM database from existing
```bash
/usr/lib/rpm/rpmdb_dump <existing> | /usr/lib/rpm/rpmdb_load <new>
```

### Rebuild the database
```bash
rpm --rebuilddb
```

## Kernel Modules

### List loaded modules
```bash
lsmod
```

### List module options
```bash
modinfo -p <module>
```

### Load a module
```bash
modprobe <module> <parameters>
```

### Remove a module
```bash
modprobe -r <module>
```

### View loaded module parameters
```bash
cat /sys/module/<module>/parameters/<parameters>
```

## File System Issues

### Recover an XFS filesystem
```bash
xfs_repair /dev/<filesystem>
```

### Recover an ext4 filesystem
```bash
fsck.ext4 /dev/<filesystem>
```

### List LVM archived metadata
```bash
vgcfgrestore -l <volume group>
```

### Undo an LVM change
```bash
vgcfgrestore -f <file from metadata> <volume group>
```

### Deactivate a logical volume
```bash
lvchange -an /dev/<volume group>/<logical volume>
```

### Activate a logical volume
```bash
lvchange -ay /dev/<volume group>/<logical volume>
```

## iSCSI

### Configuration
Ensure the initiator name matches ACL in `targetcli`:
```bash
/etc/iscsi/initiatorname.iscsi
```

### Security settings
```bash
targetcli | /etc/iscsi/iscsid.conf
```

### Discovery targets
```bash
iscsiadm -m discovery -t sendtargets -p <host>
```

### List targets
```bash
iscsiadm -m node
```

## Networking

### DNS Verification
Check configuration and resolve potential conflicts:
```bash
cat /etc/resolv.conf  # Check for valid DNS host
cat /etc/nsswitch.conf  # Check NSS settings
cat /etc/hosts  # Verify for collisions
```

### Connectivity Testing
```bash
telnet <host> <port>  # Test connectivity
nc -l <port>  # Listen on a port
```

## Firewalld

### List running configuration
```bash
firewall-cmd --list-all
```

### Add firewall rule
```bash
firewall-cmd --permanent --add-port=<tcp port>/tcp && firewall-cmd --reload
```

## Packet Captures

### Read a packet capture
```bash
tcpdump -r <file>
```

### Read a packet capture and display ASCII values
```bash
tcpdump -Ar <file>
```

### Create a packet capture
```bash
tcpdump -i <interface> -w <file>
```

## Application Troubleshooting

### List libraries used by an application
```bash
ldd $(which <application>)
```

### Test for memory leaks
```bash
valgrind <application>
```

### Debug application
Example: tracing open and close system calls in `ls`:
```bash
strace -e open,close ls
```

## SELinux

### Use `sealert` on audit log
```bash
sealert -a /var/log/audit/audit.log
```

### List SELinux booleans
```bash
getsebool -a
```

### Set a permanent boolean
```bash
setsebool -P <boolean> <0|1>
```

### Restore context of file/dir
```bash
restorecon -Rv <file/dir>
```

## Authentication

### View LDAP/Kerberos settings
```bash
cat /etc/sysconfig/authconfig
```

### Modify LDAP/Kerberos settings
```bash
authconfig-tui
```

### Obtain a Kerberos ticket
```bash
kinit
```

### List Kerberos cache
```bash
klist
```

## SystemTap

### Run a script
```bash
stap <script>
```

### Compile a script into a module
The `-p 4` flag compiles the script with optimizations:
```bash
stap -p 4 -m <module name> <script>
```

### Setup SystemTap module
```bash
mkdir -p /lib/modules/$(uname -r)/systemtap
mv <module> /lib/modules/$(uname -r)/systemtap/
staprun <module>
```

### Grant user access to run SystemTap modules
```bash
usermod -aG stapusr <user>
```

