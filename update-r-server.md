# Updating the R-Server Studio on Ubuntu 

## Updating R

To make sure that you are running the latest version of r, remove old R-base

```
sudo apt purge r-base r-recommended r-cran-*
sudo apt autoremove
sudo apt update
sudo rm -Rf /usr/local/lib/R/site-library
```

Then install the latest version of R 

```
sudo apt install r-base r-base-core r-recommended
```

## Latest R Ubuntu packages

To obtain the latest R 3.6 packages, add the following line to `/etc/apt/sources.list`:

```
deb https://cloud.r-project.org/bin/linux/ubuntu xenial-cran35/
```

(note: the option here is xenial i.e Ubuntu 16. See [this link](https://cran.rstudio.com/bin/linux/ubuntu/README.html) for other versions.
For Ubuntu 18, replace “xenial” with “bionic” in the link above)


## Udating RStudio Server

Check for active sessions on the server

```
sudo rstudio-server active-sessions
```

If any, suspend all sessions

```
sudo rstudio-server suspend-all
```

Put the old RServer version into offline mode

```
sudo rstudio-server offline
```

Download package and Install package

```
sudo apt-get install gdebi-core
wget https://download2.rstudio.org/server/xenial/amd64/rstudio-server-pro-1.3.1093-1-amd64.deb
sudo gdebi rstudio-server-pro-1.3.1093-1-amd64.deb
```

Check the R Server status

```
sudo systemctl status rstudio-server
```