# CSMM docker install

<https://catalysm.net/discord>
<https://docs.csmm.app/>

## Installing CSMM

You should not run CSMM as root! Instead, create a separate user & switch to the home directory of that user.

```bash
sudo adduser csmm
```

```bash
su - csmm
```

Get the latest files from Github and install the dependencies.

```bash
cd /home/docker
```

```bash
git clone https://github.com/CatalysmsServerManager/7-days-to-die-server-manager
```

```bash
mv 7-days-to-die-server-manager csmm
```

#### WARNING

At this point, you should set up your env file! [Follow the instructions](https://docs.csmm.app/en/CSMM/self-host/configuration.html)

## Configuring CSMM

CSMM uses a special .env file, this file gets read on start up and the values become environment variables. Let's start by gathering these variables

#### Steam API key

Go to the [Valve API key page (opens new window)](https://steamcommunity.com/dev/apikey)and register a key.

#### Discord bot account

Go to the [Discord developer page (opens new window)](https://discordapp.com/developers/applications)and create a new application. You must also make this application a bot account. For more info, see [this guide by Reactiflux (opens new window)](https://github.com/reactiflux/discord-irc/wiki/Creating-a-discord-bot-&-getting-a-token)or Google.

Collect the following values from the page:

- Client ID
- Client secret
- Bot token

If you want to sync roles between CSMM and Discord, make sure you enable the server members intent

![server members intent](https://docs.csmm.app/assets/images/CSMM/discordIntegration/serverMembersIntent.png)

Finally, you must also set a redirect URL for Oauth2 authentication. This is done in the "Oauth2" section of Discord.

![Discord auth redirect](https://docs.csmm.app/assets/images/CSMM/discordIntegration/discord-redirect.png)

Make sure you use your CSMM domain here. If your domain were example.com, the Oauth2 redirect URL would be `https://example.com/auth/discord/return`. This is required to be able to link your Discord profile to CSMM.

## .env file

Copy the example file to a new file

```bash
cp .env.example.docker .env
```

```bash
nano .env
```

DBSTRING, a [special syntax (opens new window)](https://sailsjs.com/documentation/reference/configuration/sails-config-datastores#?the-connection-url)is used. protocol://user:password@host:port/database

CSMM_HOSTNAME is used to generate links, CORS and other things. You should set this to the ip/domain you will access CSMM from. Do NOT add a trailing slash to this.

Redis configuration uses a similar system to the MySQL connection string. redis://:password@127.0.0.1:6379 or redis://127.0.0.1:6379 (if you did not set a password)

Your .env file should look similar to this

```makedown
CSMM_HOSTNAME=http://localhost:1337
CSMM_LOGLEVEL=info
CSMM_PORT=1337

# This overrides the default donator check
CSMM_DONATOR_TIER=enterprise
# How often CSMM will check for new logs
CSMM_LOG_CHECK_INTERVAL=3000
# How many logs CSMM will gather per request
CSMM_LOG_COUNT=50

# Comma separated list of steam IDs for users that get extended control, uncomment and add your own IDs
CSMM_ADMINS=76561198028175941,76561198028175941


# External APIs

API_KEY_STEAM=FSQO42FQSF878FSV89B4C3AFSP789423VDDE0
DISCORDOWNERIDS=293112752531308544
DISCORDBOTTOKEN=MzI0ODQzMDUzOTIxODYxNjM0.DTjClA.Y8kzPIq2kSWZmh5SAIAp5VOTcO4
DISCORDCLIENTSECRET=pfsqFpfvPfqs4562V-OFJSvpqscl487qszmL
DISCORDCLIENTID=19846168795143546


# Invite link for the dev server
INVITELINK=


# If true, only people in CSMM_ADMINS can add servers
CSMM_PRIVATE_INSTANCE=true

# If true, only donators can add servers
CSMM_DONOR_ONLY=false


###############
# DATASOURCE CONNECTION STRINGS
# Make sure you use the vars for your install method and delete the others
###############
# This initializes the DB container, see https://hub.docker.com/_/mariadb
MYSQL_USER=csmm
MYSQL_PASSWORD=mysecretpasswordissosecure
MYSQL_DATABASE=csmm
MYSQL_RANDOM_ROOT_PASSWORD=true
DBSTRING=mysql2://csmm:mysecretpasswordissosecure@db:3306/csmm
REDISSTRING=redis://cache:6379



# Importing
# When this is set, CSMM will try and find json files in this location and import them as servers
CSMM_IMPORT_FROM_DIR=/path/to/somewhere
```

## Docker installation on Linux

Copy the Docker compose file to your machine.

```bash
cp docker-compose.yml docker-compose.yml.old
```

To use the already-built images, comment the `build` line and uncomment the `image` line

```markdown
    #build: .
    image: catalysm/csmm
```

If you want to build the image yourself locally, you can keep the config.

At this point, you should set up your env file! [Follow the instructions](https://docs.csmm.app/en/CSMM/self-host/configuration.html) and then come back and continue following these steps.

TIP

Make sure to use the .env.example.docker as base for your config. You should use docker networking to route CSMM to other services as well.

```markdown
DBSTRING=mysql2://csmm:your-db-password@db:3306/csmm
REDISSTRING=redis://:your-redis-password@cache:6379
```

Once you have your .env file ready to go, you can start CSMM with `docker-compose up -d`. Have a look at the logs to see if everything has started correctly with `docker-compose logs -f`

TIP:
You should set up a [reverse proxy](https://docs.csmm.app/en/CSMM/self-host/reverse-proxy.html) and enable HTTPS!

#### Start CSMM with

```bash
docker compose up -d
```

Have a look at the logs to see if everything has started correctly with:

```bash
docker compose logs -f
```

To upgrade the containers, you must pull the new version:
Check in your docker-compose.yml what image is specified (eg image: catalysm/csmm:v1.20) and adjust as needed.

```bash
docker compose pull
```

```bash
docker compose down && docker compose up -d
```

If you'd like to force a pull you can use

```bash
sudo docker pull catalysm/csmm:${version}
```
