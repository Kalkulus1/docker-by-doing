# Storing Container Data In Docker Volumes

## Discover Anonymous Docker Volumes
Check the docker images:
```sh
docker images
```

Run a container using the postgres:12.1 repository:
```sh
docker run -d --name db1 postgres:12.1
```

Run a second container using the same image:
```sh
docker run -d --name db2 postgres:12.1
```

Check the status of the containers:
```sh
docker ps
```

List the anonymous volumes:
```sh
docker volume ls
```

Inspect the db1 container:
```sh
docker inspect db1 -f '{{ json .Mounts }}' | python -m json.tool
```

Inspect the db2 container:
```sh
docker inspect db2 -f '{{ json .Mounts}}' | python -m json.tool
```

Create a third container using the --rm flag:
```sh
docker run -d --rm --name dbTmp postgres:12.1
```

Check the status of the container:
```sh
docker ps -a
```

List the anonymous volumes:
```sh
docker volume ls
```

Stop the db2 and dbTmp containers:
```sh
docker stop db2 dbTmp
```

List the anonymous volumes:
```sh
docker volume ls
```

Check the status of the containers:
```sh
docker ps -a
```



## Create a Docker Volume
Create a Docker volume:
```sh
docker volume create website
```

Verify that the volume was created successfully:
```sh
docker volume ls
```

Copy the widget-factory-inc data to the website container:
```sh
sudo cp -r /home/cloud_user/widget-factory-inc/web/* /var/lib/docker/volumes/website/_data/
```

List the copied data:
```sh
sudo ls -l /var/lib/docker/volumes/website/_data/
```


## Use the Website Volume with Containers
Run a docker container with the website volume:
```sh
docker run -d --name web1 -p 80:80 -v website:/usr/local/apache2/htdocs:ro httpd:2.4
```

Check the status of the container:
```sh
docker ps
```

In a web browser, verify connectivity to the container:
```sh
<PUBLIC_IP_ADDRESS>
```

Run a second container with the --rm flag:
```sh
docker run -d --name webTmp --rm -v website:/usr/local/apache2/htdocs:ro httpd:2.4
```

Check the status of the containers:
```sh
docker ps
```

Stop the webTmp container:
```sh
docker stop webTmp
```

Check the status of the containers:
```sh
docker ps -a
```

Verify that the website can still be accessed through a web browser:
```sh
<PUBLIC_IP_ADDRESS>
```


## Clean up Unused Volumes
Clean up the unused volumes:
```sh
docker volume prune
```

Check the currently running containers:
```sh
docker ps -a
```

Remove the db2 container:
```sh
docker rm db2
```

Clean up the unused volumes again:
```sh
docker volume prune
```

List the current volumes:
```sh
docker volume ls
```


## Back up and Restore the Docker Volume
Switch to the root user:
```sh
sudo -i
```

Find where the website volume data is stored:
```sh
docker volume inspect website
```

Copy the Mountpoint
Back up the volume:
```sh
tar czf /tmp/website_$(date +%Y-%m-%d-%H%M).tgz -C /var/lib/docker/volumes/website/_data .
```

Verify that the data was backed up properly:
```sh
ls -l /tmp/website_*.tgz
```

List the contents of the tgz file:
```sh
tar tf /tmp/<BACKUP_FILE_NAME>.tgz
```

Exit out of root:
```sh
exit
```

Run a new container using the website volume, and create a backup:
```sh
docker run -it --rm -v website:/website -v /tmp:/backup bash tar czf /backup/website_$(date +%Y-%m-%d-%H-%M).tgz -C /website .
```

Verify that the data was backed up properly:
```sh
ls -l /tmp/website_*.tgz
```

Switch to the root user:
```sh
sudo -i
```

Change to the /docker/volumes/ directory:
```sh
cd /var/lib/docker/volumes/
```

List the volumes:
```sh
ls -l
```

Change to the website/_data directory:
```sh
cd website/_data/
```

Remove the contents of the directory:
```sh
rm * -rf
```

Verify that the backups are still available:
```sh
ls -l /tmp/website_*.tgz
```

Restore the data to the current directory:
```sh
tar xf <BACKUP_FILE_NAME>.tgz .
```

Verify that the data was restored successfully:
```sh
ls -l
```
