Librehealth Community Infrastructure Ansible Playbook
======================

# A note about this repo
There are files which are encrypted using [git-crypt][], in order to work with this, you must have a published PGP key.

Ansible will not work with the files in an encrypted state.

## Our Plays


### Common Play (`common.yml`)
This play runs the following roles which are common with all servers, regardless of the Tech Stack used. All plays include this.

This includes:

- Setting up users
- installing sudo
- installing sshd
- configuring ufw
- install docker/docker-compose
- configuring ntp for clock synchronization
- setting the timezone (UTC)


### Datadog Play (`datadog.yml`) (included in the Common Play)
Installs the [datadog agent](https://datadog.com) and setting up checks. This is run first and is included in the Common Play.

This play is encrypted.

### PostgresSQL Play (`postgres.yml`) (included in the Common Play)
Installs and configures PostgresSQL for hosts that need it. This is only run if needed on the given host. It is included in the Common Play.

### Certbot Play (`certbot.yml`)
Provisions and renews [Let's Encrypt SSL Certificates](https://letsencrypt.org) using [Certbot](https://certbot.eff.org/).

Install Certbot if it is not installed on the server.

### LibreHealth EHR (`lh-ehr.yml`)
This sets up and installs dependencies for LibreHealth EHR.

It installs Apache, PHP, Composer, and Imagemagick.


### LibreHealth Toolkit/Radiology (`lh-tomcat.yml`)
This sets up and installs nginx, java, and tomcat.

## Requirements
* This repo.
* ansible  2.1+ installed on the same machine the repo is cloned to.

## How to use this
How this is run is dependent on what project you are working with.
### For LibreHealth Toolkit/Radiology

#### LibreHealth Radiology
`ansible-playbook -i inventory/all -l radiology lh-tomcat.yml`

#### LibreHealth Toolkit
`ansible-playbook -i inventory/all -l toolkit lh-tomcat.yml`

#### For Both LibreHealth Toolkit and Radiology
`ansible-playbook -i inventory/all -l tomcat lh-tomcat.yml`

### For LibreHealth EHR
`ansible-playbook -i inventory/all -l ehr lh-ehr.yml`

## Running example ad-hoc commands

#### Update all packages playbook
This will do a dry run of updating all packages on all servers

`ansible-playbook -i inventory/all update.yml --check -v`

When satisfied this will not breaking anything drop the `--check` and it will update all servers in the inventory.

#### Update a certain package to latest version

`ansible -i inventory/all all -m apt -a "update_cache=yes name=openssl state=latest" --become`

This will only update the package to the latest version if it is already installed.  It will not install the openssl package on hosts that do not have it installed.

[git-crypt]: https://github.com/AGWA/git-crypt