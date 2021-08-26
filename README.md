[![Build Status](https://drone.element-networks.nl/api/badges/Element-Networks/ansible-role-docker_services/status.svg)](https://drone.element-networks.nl/Element-Networks/ansible-role-docker_services)

# Docker services catalogue
This role contains a collection of services that run on a Docker Swarm, each service is segmented in it's own (set of) network(s). And all persistent storage is bind-mounted into the container, this allows for placing that data on shared storage, such as NFS or GlusterFS.

The catalogus currently contains:

* Drone: a CI/CD controller with agents (https://github.com/drone/drone)
* Gitea: Go powered Git server with webGUI (https://github.com/go-gitea/gitea)
* Portainer: Docker management inferface (https://github.com/portainer/portainer)
* AWX: Ansible Tower's upstream (https://github.com/ansible/awx)
* Traefik: Docker integrated reverse proxy (https://github.com/containous/traefik)
* Docker registry: local docker image storage
* Janitor: a docker that will periodically run a script to clean up all unused and outdated stuff
* Mediawiki: Wikipedia's wiki engine, check the instructions below.
* DB_backup: a docker that will periodically run a script to tell all mariadb containers found to dump their databases on the shared storage
* Kanboard: A Kanban board with a webinterface
* Factorio server: A game where you manage a automated factory and defend it against the neighbors
* Minecraft server: A game where you can build things and fight the dragon in the Netherrealm!
* Teamspeak server: VOIP server
* Rocket chat: A selfhosted alternative to Slack and others
* PeerTube: A free and decentralized alternative to video platforms


This role has a few mandatory parts that will be provisioned which (most, if not all) other services depend upon:

* Portainer
* Janitor
* Traefik

# Deployment
In order to use the services from this catalogue, do the following:

* Install the ```community.docker``` collection on your Ansible controller
* Install docker (tip: use Jeff Geerlings' Docker role)
* Create the {{ docker_data_dir }}, preferably on shared storage
* Copy ``` defaults/main.yml ``` and configure it
* Run the role with a playbook such as:

```
---
- hosts: 'docker'
  roles:
    - 'docker_services'
```

# Reaching services
After provisioning all services will be reachable under the following name:

```
https://{{ service_app_name }}.{{ traefik_domain }}
```

Which translates to something like https://portainer.dckr.example.com

# Extra actions
Some services require some more actions before they work, below is are the instructions per service if they require additional setup.

## Mediawiki
Please make sure you create a local copy of LocalSettings.php in {{ docker_data_dir }}/mediawiki_data, this copy will be mounted inside the contianer.

This playbook will check if the file is present, but will not change the content.

## DB Backup
The DB backup service is rather simple, it will only backup all containers that match the filters:

* container name ends with mariadb

It will NOT manage your backups, it will create a backup.sql in {{ docker_data_dir }}/{{ app_name }}_db and overwrite it every time.
