# R Server
(Write a script to do this whole thing!!)

The R server is a VM running on thing5

> __IP Address:__ 162.210.90.35

`r.cs.stolaf.edu` - on sign in shows list of projects being worked on 
See if the server is down or logging in is working or stuff like that

`/dev/vda1` - ran out of space
`/dev/vdb1` - has user home folders

`/home` - list of everyone who has taken a class and/or has an account
`/cir` - cir teams working on projects

## Handling a Common request for a Directory in `/cir`

- Check if all users have accounts: 
  - `etc/passwd | grep <username>`
- If not, find their user Id:
  - `ldapsearch -LLL -H “ldaps://ad.stolaf.edu” -D “cn=<your_username>,ou=stoUsers,dc=ad,dc=stolaf,dc=edu” -W -b “cn=<user_to_create>,ou=stoUsers,dc=ad,dc=stolaf,dc=edu” | grep Uid`

#### Need:
- Cluster manager credentials
- User you want to create
- Group id is UID + 1000

```bash
sudo groupadd -g <group ID> <username>                          # First add the group (uid + 1000)
sudo useradd -m -u UID -g groupID -s /bin/bash <username>       # Then create the user
```

Pick a name for the group that will be the same as the folder

```bash
sudo groupadd <project_name>
usermod -aG <group_name> <user_name>            # do this for every user who needs access 
```

Now that users are added, add a folder directory for them 

```bash
cd /cir
mkdir <project_name>                # should be same as group name
```

Set proper permissions

```bash
sudo chown :<project_name> /cir/<project_name>
sudo chmod 2770 <project_name>      # 7 means read write and execute, giving the owner and group the access to do that; 2 is the setgid bit
```

Link it to their home directories

```bash
ln -s /cir/<project_name> /home/<username>/     # do this for every user who needs access
ln -s means symbolic link
```

### Restart the R Server

__Try to avoid rebooting the vm__

```bash
sudo systemctl restart rstudio-server.service
```

## Other Helpful commands

```bash
rstudio-server                      # gives all the commands you can do
rstudio-server active-sessions      # can see all that are logged in
rstudio-server suspend-session PID  # can quit someone's session for them (can also force-suspend-session or kill-session)
```

*Need to restart the server for the system certificate updates

```bash
cd /home/<username>
ls -la                              # shows hidden files
```

Can remove .Rhistory and .rstudio to make them like a new user, fresh start (without removing any files)
Recommended you back these up first

** look up acl commands:
	getfacl and setfacl
