# Google Multi-Factor Authentication

## Step 1: Install GA 

Note: using `sudo` will install the package for all users and other users will be locked out of their accounts.

```
apt install libpam-google-authenticator
```

Then run `google-authenticator` to create a new secret key in your home directory.

When asked “Do you want authentication tokens to be time-based?” Answer y.
Download Google Authenticator on your phone.
Scan the QR code using the mobile app.

## Step 2: Configure SSH Daemon to Use Google Authenticator

Open the SSH server configuration file.

```
sudo nano /etc/ssh/sshd_config
```

Make sure `UsePAM` and `ChallengeResponseAuthentication` are both set to yes like this:

```
UsePAM yes
ChallengeResponseAuthentication yes
```

Save and close `/etc/ssh/sshd_config`. Then restart the SSH daemon.

```
sudo systemctl restart ssh
```

Next, edit the PAM rule file for the SSH daemon.

```
sudo nano /etc/pam.d/sshd
```

Add the following entry at the end of the file.

```
auth required pam_google_authenticator.so
```

[MFA on Ubuntu](https://www.linuxbabe.com/ubuntu/ssh-two-factor-authentication-ubuntu-16-04-google-authenticator#:~:text=Step%201%3A%20Install%20and%20Configure,the%20default%20Ubuntu%20package%20repository.&text=Then%20run%20the%20google%2Dauthenticator,key%20in%20your%20home%20directory.)