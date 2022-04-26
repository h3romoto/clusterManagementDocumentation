# Stogit

## Intro

Stogit is the GitLab server at St. Olaf.
Students in most CS classes use it to submit their code for homeworks and projects.
It is also used for Cluster Management and Hipercic to store code and other important documents.
CKubed uses it to pull container images that run on our Kubernetes cluster.
We also have GitLab Runners running on some machines (like Thing3) that are used for CI/CD in courses and hipercic.

Read more about [GitLab](https://about.gitlab.com/stages-devops-lifecycle/)

[GitLab Documentation](https://docs.gitlab.com/ee/README.html)

[GitLab Runner Docs](https://docs.gitlab.com/runner/)

## Structure

On Thing5, there is a VM called StoGit.
Standard SSH with a password will not let you into the machine.
You need to SSH into Thing5, then access the machine with Virt-Manager.

Once in the machine, run `docker ps`.

The GitLab is a container running on the VM with ports 22, 80 and 443 bound to the same ports on the VM.
Stogit is the webserver that runs on the container.

Run `docker inspect stogit`

This shows you details about the container.
It mounts `/opt/stogit/data`, `/opt/stogit/config` and `/opt/stogit/logs` from the VM.
Those folders are in turn mounted on the VM from atlas.
Make sure those mounts are always there.

The file `/opt/stogit/config/gitlab.rb` is the configuration file for stogit.
LDAP Authentication on StoGit is also set up in that file.

As of now, we run Enterprise Edition GitLab, so the container image should be tagged with "ee" and NOT "ce" (which was what we ran before).

## Backups 

Before each upgrade, it important to backup our data just in case something goes wrong during or after the update.

To backup the data, run this command on the VM:
`sudo docker exec -t stogit gitlab-backup create`

As of Jan 2021, this process takes about an hour and a half. 

Gitlab might ask you to back up some files manually. For example /opt/stogit/config/gitlab.rb and /opt/stogit/config/gitlab-secrets.json. 
You can back those files up by copying them over to the /opt/stogit/data/backups which is mounted from atlas (162.210.90.4)

After the backing up is done, you want to check if the background migrations are complete.
This is especially important if you are performing back-to-back [updates](#back-to-back-updates).

To check the background migrations, run this command:
`docker exec -t stogit gitlab-rails runner -e production 'puts GitLab::BackgroundMigration.remaining'`

If the migrations are complete, the terminal will display a zero on the next line.
Otherwise, you should wait a little longer. 

## Upgrade StoGit

Before you begin, make sure to read the section about [backups](#backups).

Look at `/opt/stogit/run.sh`.
That script handles updating stogit to the latest version.
The file `/opt/stogit/stogit-config-variables` contains variables required in the script.

The script, `/opt/stogit/run.sh`:
- pulls the `STOGIT_CONTAINER_IMAGE` image,
- stops (if necessary) the running container with the name `stogit`,
- removes that container (to free space) via `docker rm`,
- then starts the StoGit image using its default configuration format.

The script,`/opt/stogit/stogit-config-variables`, declares:

| variable                             | description                                                  | value                                |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------------ |
| `STOGIT_CONTAINER_NAME`              | The name to be used by `docker run` as the container name.   | `stogit`                             |
| `STOGIT-CONTAINER-HOSTNAME`          | The `hostname` to be used for the Docker container, which in turn gets resolved by GitLab and used internally. | `stogit.cs.stolaf.edu`               |
| `STOGIT_CONTAINER_IMAGE`             | The image to run for StoGit.                                 | `gitlab/gitlab-ee:latest`            |
| `STOGIT_CONTAINER_SSH_EXTERNAL_PORT` | The port to be used for SSH externally                       | `22` (the host's SSH port is `9922`) |

### About Versions

To update to the latest version, just execute the script.

To update to a specific version, you would have the version in `/opt/stogit/stogit-config-variables`.
The version syntax looks something like this example: `13.7.0-ee.0`

#### Back-to-back updates

In the case that GitLab is outdated and behind by multiple updates, you should check if there are intermediate updates that you are required to install. [YOU SHOULD READ THIS DOCUMENTATION](https://docs.gitlab.com/ee/update/index.html#version-specific-upgrading-instructions) 

For example, GitLab recommended 6 upgrades when Stogit was upgraded from 12.0.2 to 13.7.0 back-to-back.
These intermediate steps differ with each version so be sure to check [this page](https://docs.gitlab.com/ee/update/README.html#upgrade-paths) for more information. 

Make sure StoGit is running after each upgrade by checking the container is up and running (with `watch sudo docker ps`).
Check the [website](https://stogit.cs.stolaf.edu/) each time as well.

## Performing the Upgrade

When you are ready to start the upgrade, run the `/opt/stogit/run.sh` script with superuser privileges.
StoGit will update and restart.

The startup will take ~5 minutes, and progress can be tracked by running `watch sudo docker ps`.
After ~5 minutes, it may say that the container is unhealthy, this should change to healthy after another minute or so.
Going to the StoGit website may show a "Deploy in progress" page, this should go away after a couple minutes.

You can check the help page (accessed via the menu with a ? in the top right corner) to view the current version.
This is also where you should check if StoGit needs updating or not, as there is an indicator next to the version that will tell you if it is "up-to-date" or if there is an "update available" or if it needs an "update asap".

## Deleting Old Stogit Docker Images 
Currently, there is not a ton of storage on the Stogit Virtual Machine so we recommend deleted the oldest docker image on the VM once the update is successfully done:

List the available stogit docker images:
`sudo docker images`

Take note of the stogit docker image ID that is at the bottom of the list. You'll need the ID to back up and delete the image.
Run the following command: 
`sudo docker save -o /opt/stogit/data/backups/stogit_image_backup_{$stogit_image_ID} $stogit_image_ID; sudo docker rmi $stogit_image_ID`

This will take a minute or two. 

## GitLab Runners

As of Feb 2021, there are two GitLab runners:

- A docker container on Thing 3, running most jobs
- A binary installation on a Raspberry Pi in the Server Room, running jobs for the HD course

The GitLab runner has to be the same minor version as the GitLab instance.
Both should be updated otherwise the runner will not be able to check for jobs.
See [here](https://docs.gitlab.com/runner/) for more about GitLab runners.

### Upgrading the Thing3 Runner

There is a script already created for upgrading the GitLab runner, similar to how StoGit is upgraded.

Run the `/opt/stogit-runner/run.sh` script with sudouser privileges.

To confirm the upgrade was completed successfully, run (or retry) a job in any repository that doesn't actually matter and check the version at the top of the logs (listed in the form "Running with gitlab-runner $VERSION").

### Upgrading the Raspberry Pi Runner

To upgrade the GitLab runner, run these commands:

1. `sudo gitlab-runner stop`
... which stops the GitLab runner application service

2. `sudo curl -L --output /usr/local/bin/gitlab-runner "https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm"`
... which downloads the new binary

3. `sudo chmod +x /usr/local/bin/gitlab-runner`
... which grants you execution permissions

4. `sudo gitlab-runner start`
... which starts the service

You can check the status of the service by executing:
`sudo gitlab-runner status`

### Adding a New GitLab Runner

See [Install GitLab Runner](https://docs.gitlab.com/runner/install/).

### Registering a GitLab Runner

Once you have a runner set up and installed, you need to register it with StoGit so it can be used.
Runners can be registered with all of StoGit, or with a specific repository or group.

See [Registering Runners](https://docs.gitlab.com/runner/register/).
