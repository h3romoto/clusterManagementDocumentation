# Manage SUDO on machines

## Step 1

Take away all current sudo permissions unless you know that they need it (we can change once they ask for it)

`$ sudo vigr`		- remove all users for sudo group except bw-admin

`$ sudo vigr -s` 	- remove all users for sudo gshadow except bw-admin

## Step 2

Make users for current cluster managers (if ldap is not setup, it has to be done before this)

`$ git clone https://stogit.cs.stolaf.edu/bw/scripts.git`

`$ cd scripts`

`$ for usr in $(printf "de1\nberger6\nnarvae1\npaudel2\nkokala1\ncassid1\nkhondo1\ntran13\nrab"); do sudo ./makeuserIT.sh $usr; done`

## Step 3

Create new sudoers file with permissions for leads and rab

`$ sudo visudo -f /etc/sudoers.d/managed`

```
# This file is managed by St. Olaf Lead Cluster Managers
# Use this to give users privileged access to machines
# instead of adding them to the sudo group
# Create ldap user before adding them here to the appropriate alias
# Uncomment lines when users are added
# Always follow the principle of least privilege

# Aliases
# Lead Cluster Managers
User_Alias      LEADS = de1, narvae1, berger6

# Non-Lead Cluster Managers (only who need access)
#User_Alias     NONLEADS =

# Access
LEADS   ALL = ALL
rab     ALL = ALL
```

Read up on what this means [here](https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file).

Add people if you currently know that they necessarily need sudo.
We can always add people once they request it even if their permissions are taken away now.

## Step 4

When adding people other than leads and rab, try not to give ALL permissions.
Instead, give permissions to specific commands.
For example, giving permissions for `useradd` and `groupadd` commands to non-lead cluster managers to add new users.

For example:

```
tran13	ALL = /usr/sbin/useradd, /usr/sbin/groupadd
```