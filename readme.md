# HR Bot

## Requirements
- Docker
    - [Docker for Mac](https://docs.docker.com/docker-for-mac/)
    - [Docker for Linux](https://docs.docker.com/engine/installation/linux/ubuntulinux/)
- PHP >= 7.13
- Mysql 5.7
- Laravel ~5.6
- [Redis](https://github.com/antirez/redis)
- npm or yarn (recommend)

## Installation

We use Docker and Docker Compose for constructing the development environment of the application. Therefore, we first need to install these two softwares:

- Install Docker: https://docs.docker.com/install
- Install Docker Compose: https://docs.docker.com/compose/install

Make sure `docker` and `docker-compose` commands are available in current shell.

In order to run `docker` command without **sudo** privilege:
- Create **docker** group if it hasn't existed yet: `sudo groupadd docker`
- Add current user to **docker** group: `sudo gpasswd -a ${whoami} docker`
- You may need to logout in order to these changes to take effect.

Change current directory to application code folder and run the following commands:
- Copy file `.env.example` to `.env`, `docker-compose.yml.example` to `docker-compose.yml`
- Start up docker containers: `docker-compose up -d`. To stop docker containers, using `docker-compose stop` command
- Change to workspace environment: `docker exec -it love_workspace bash`

Inside workspace container, run the following commands:
- Install composer packages: `composer install --no-suggest`
- Change permission for some directories: `chmod -R 777 storage/ bootstrap/`
- Seed the database: `php artisan db:seed`

If you update these environment variables, you must restart docker containers in order to those changes to take effect.

- Site will publish on 127.0.0.1:{`ports`} (`ports` config in docker-compose.yml > services > ngix > ports). Add domain to host file so we can access site by domain:{`ports`}
```
127.0.0.1 love.local
127.0.0.1 admin.love.local
```

By default, port 80 of NGINX container is mapped to port 4040 of the host machine. If this port is currently used by another application, you can change that port by editing `docker-compose.yml`.

### Deployment

*Quick deploy*: Run after each time you pull code from framgia repository
```BASH
Run deploy
```

- Install laravel
```BASH
composer install
php artisan key:generate
```

- Install node modules
```BASH
npm install
#or
yarn install
```

- Build
```BASH
npm run dev
#or
yarn run dev
```

- Run migration
```BASH
# Check Docker Container list, copy the `workspace` container name
docker ps

# Go into the `workspace` container
docker exec -it love_workspace bash

# Run migration
php artisan migrate --seed

# Or running outside the docker container
docker exec -it love_workspace php artisan migrate --seed
```

- If you want run project on your local instead of Docker, just skip all step about docker and create virtual host. And modify `.env` config of `DB_HOST`, `DB_HOST_TEST`, `REDIS_HOST` to `127.0.0.1`

# Get Google Access Token

Create new client with type "Other", then save file credentials in path 'storage/google/credentials.json'.

Run command
`php artisan google:get-token`

Open the link on the screen to the browser, then copy code receive from browser in command. If successful, the token file will be saved in path 'storage/google/token.json'

 # Add Seeder Chatwork User
 Data chatwork user contains in file 'config/chatwork-users.php'

 # Add hook to user Bot Chatwork
 Add hook to link `{host}/api/chatwork/hook`
 # Setup queues
 Run command
 `php artisan queue:table`
 `php artisan migrate`
 `php artisan queue:work`
 # Setup cron job
 Run command
 ```BASH
 crontab -e
 ```
 Add to file new line with content

 `* * * * * docker exec love_workspace php artisan schedule:run >> /dev/null 2>&1`

 Replace `love_workspace` with name your workspace.

 And restart cron:
 ```BASH
 sudo service cron restart
 ```

