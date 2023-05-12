setup csmm
	
	cd /home/docker
	git clone https://github.com/CatalysmsServerManager/7-days-to-die-server-manager
	mv 7-days-to-die-server-manager csmm
	
edit .env docker-compose.yml and Caddyfile

start CSMM with: 
	
	docker compose up -d
	
Have a look at the logs to see if everything has started correctly with:
	
	docker compose logs -f
	

To upgrade the containers, you must pull the new version.

Check in your docker-compose.yml what image is specified (eg image: catalysm/csmm:v1.20) and adjust as needed
	docker compose pull
	docker compose down && docker compose up -d
	
If you'd like to force a pull you can use

	sudo docker pull catalysm/csmm:${version}
	
	https://steamcommunity.com/dev/apikey
https://discord.com/developers/applications


-----------------------------------------------------------------------
