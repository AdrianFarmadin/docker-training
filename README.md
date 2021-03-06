## Run, Start, Stop...

```
$ docker ps
$ docker pull alpine
$ docker image ls
$ docker run alpine echo “hello from alpine”
$ docker run alpine ls -l
$ docker ps -a
$ docker start CONTAINER
$ docker container rm CONTAINER
```

Try another command.
```
$ docker run alpine /bin/sh
```

Not working? 
You need to attach an interactive tty in the container with the `-it` flags.
```
$ docker run -it alpine /bin/sh
```

Let's start something more interesting.
```
$ docker run --name mariadb -p 3306:3306 -e MYSQL_ROOT_PASSWORD=super-secret-pw -e MYSQL_DATABASE=todo -e MYSQL_USER=todo -e MYSQL_PASSWORD=todo --rm -d mariadb
```

## Network
```
$ docker network create example
$ docker network connect --alias db example mariadb
$ docker run --rm -p 8080:8080 --network example adrianfarmadin/todo-jsf-example
$ docker run --network example -p 8090:8080 -d adminer
```

Inspect containers default aliases.

## Volumes
Why volumes?
* Selinux: chcon -Rt svirt_sandbox_file_t /my/own/datadir 
* Filesystem UserId/GroupId


```
$ docker volume create db-vol
$ docker volume ls
$ docker volume inspect db-vol
```

Use volume
```
$ docker run --name mariadb  --mount source=db-vol,target=/var/lib/mysql -e MYSQL_ROOT_PASSWORD=super-secret-pw -e MYSQL_DATABASE=todo -e MYSQL_USER=todo -e MYSQL_PASSWORD=todo --network example --net-alias db --rm -d mariadb
```

or
```
$ docker run --name mariadb  -v db-vol:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=super-secret-pw -e MYSQL_DATABASE=todo -e MYSQL_USER=todo -e MYSQL_PASSWORD=todo --network example --net-alias db --rm -d mariadb
```

### Backup a container

```
$ docker run --rm --volumes-from mariadb -v $(pwd):/backup alpine tar cvf /backup/backup.tar -C /var/lib/mysql .
```

Restore container from backup
```
$ docker volume create db-vol2
$ docker run -v db-vol2:/dbdata -v $(pwd):/backup alpine sh -c "cd /dbdata && tar xvf /backup/backup.tar --strip 1"
$ docker run --name mariadb  -v db-vol2:/var/lib/mysql --network example --net-alias db --rm -d mariadb
```

Docker volumes are not deleted with container. You muss clean up yourself.
```
$ docker volume rm my-vol
```

## Clean up

```
$ docker kill $(docker ps -q)
$ docker container prune
$ docker volume prune
$ docker network prune
```

or delete everything

```
$ docker system prune
```


## Docker-compose

```
$ docker-compose up -d
$ docker-compose logs -f web
$ docker-compose down
```