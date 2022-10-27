# iNethi-AzuraCast

README 

In this folder, there are three files submitted, excluding the README. The files are:
.env
docker-compose.override.yml
local_build.sh


AZURACAST INSTALLATION - Steps I took

1. Installed and ran the Inethi Environment on an Intel Nuc

2. SSH into the server and use the command below:
	sudo su -

3. Pick a directory on the server that AzuraCast can use using the commands below:
	cd master-builder
	mkdir azuracast
	cd azuracast

4. Make sure Docker or Docker-Compose is up to date by using the commands below:
	./docker.sh install-docker
        ./docker.sh install-docker-compose
        ./docker.sh install

5. To install AzuraCast a stable release of AzuraCast run the commands below:
	curl -fsSL https://raw.githubusercontent.com/AzuraCast/AzuraCast/main/docker.sh > docker.sh
	chmod a+x docker.sh
	yes 'Y' | ./docker.sh setup-release
	yes '' | ./docker.sh install

6. After the installations, you will see these files in the current folder: azuracast.env, docker-compose.new.yml, docker-compose.yml and docker.sh . To see the files use the command below:
	ls

CONFIGURATIONS ON AZURACAST AFTER INSTALLATIONS 

1. Before making configuration, stop the docker container from running using the command:
	docker compose down

2. By default AzuraCast uses port 80 and 443 but these are already used by other services on Inethi. To change the ports, change some variables in the .env file:
	a) open the .env file with your favourite editor
	b) change line three to : AZURACAST_HTTP_PORT= 8001
	c) change line four to: AZURACAST_HTTPS_PORT= 8002
	d) Save the changes

3. To make sure that AzuraCast uses an inethilocal.net subdomain, create a file called docker-compose.override.yml. Add the following lines to the file:

	version: "3"
	services:
  	  web:
    	    labels:
              - "traefik.enable=true"
              - "traefik.http.routers.azuracast.rule=Host(`${TRAEFIK_API_RULE_AZURACAST}.${inethiDN}`)"
              - "traefik.http.routers.azuracast.entrypoints=${TRAEFIK_ENTRYPOINT}"
            networks:
              - default

	networks:
          default:
            external:
              name: "${INETHI_NETWORK}"

4. Create a file called local_build.sh. The file will use make build AzuraCast and configure it to work with Inethi. In the file, paste the following lines:

	#!/bin/bash
	source ../root.conf
	source ./.env

	docker-compose config
        docker-compose up -d


5. You should now be able to access AzuraCast on your browser by using the IP Address of the server on your browser eg 192.57.168.101:8001 or azuracast.inethilocal.net .

NB: We are still trying to fix the access to AzuraCast through the URL azuracast.inethilocal.net because the Intel Nuc was giving us problems and could not connect to the UCT network which made things difficult as we could not work on finding solutions.
