# Bench configuration

## AFS
Instructions largely based on
[these instructions from MIT](http://tig.csail.mit.edu/wiki/TIG/OpenAFSOnUbuntuLinux).
After running `sudo apt update`:

```bash
sudo apt install openafs-client openafs-krb5 krb5-user openafs-modules-dkms module-assistant libpam-krb5 libpam-afs-session

# reconfigure, to get it to ask you more questions
sudo dpkg-reconfigure krb5-config openafs-client

# pick up the kernel module and restart the service
sudo modprobe openafs
sudo service openafs-client restart
```

When prompted, fill out the following values for openafs:

| Question                 | Value          |
|--------------------------|----------------|
| Default realm            | ANDREW.CMU.EDU |
| AFS Cell                 | andrew.cmu.edu |
| Cache size               | 5242880 (5GB)  |
| all the yes/no questions | yes            |

You can verify AFS is running by checking `ls /afs` and looking for a bunch of
cells. Next, set up some aliases that people expect in the andrew enviornment:

```bash
sudo fs newalias andrew andrew.cmu.edu
sudo fs newalias cs cs.cmu.edu
```

In order for those aliases to persist across reboots, put the following in
`/etc/openafs/CellAlias`:
```
andrew.cmu.edu andrew
cs.cmu.edu cs
```

To check that everything is working, try

```bash
kinit
aklog
touch /afs/andrew/usr/$USER/afs-test
```

You should be able to create the file `afs-test` in your AFS home directory
without any permission issues.

## Authentication
We'll now configure using kerberos for login.

**Keep a root shell (`sudo su`) running while you do this in case you mess up**

Add to the end of `/etc/pam.d/common-session`:
```
# Kerberos Authentication
session optional  pam_krb5.so minimum_uid=1000
```

Add to the end of `/etc/pam.d/sshd`:
```
auth  optional  pam_afs_session.so
session required  pam_afs_session.so
```

Try (in a new terminal) to ssh in, using your Andrew password. After logging in,
you should be able to run `klist` and see two tickets:
 - `krbtgt/ANDREW.CMU.EDU@ANDREW.CMU.EDU`
 - `afs/andrew.cmu.edu@ANDREW.CMU.EDU`
 
Now, you should have access to your AFS space after login.
