# Bench configuration

## AFS
Instructions largely based on
[these instructions from MIT](http://tig.csail.mit.edu/wiki/TIG/OpenAFSOnUbuntuLinux).
After running `sudo apt update`:

```bash
sudo apt install openafs-client openafs-krb5 krb5-user openafs-modules-dkms module-assistant

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

To check that everything is working, try

```bash
kinit
aklog
touch /afs/andrew/usr/$USER/afs-test
```

You should be able to create the file `afs-test` in your AFS home directory
without any permission issues.
