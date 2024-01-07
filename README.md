# docker-networking-with-containers

Let's create an internal bridge network named 'localhost' where no interface will be bound.

```bash
docker network create -d bridge --internal localhost
docker network inspect localhost
```

***Expected Output***

```bash
{
        "Name": "localhost",
        "Id": "7de6e253bd516121e33336f33cfbf95215ccfe549ad93c684fd9ffeb71339755",
        "Created": "2024-01-07T14:16:00.118052034Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.20.0.0/16",
                    "Gateway": "172.20.0.1"
                }
            ]
        }
```

## `Create a MySQL Container under the localhost Network`

```
docker run -d --name mysql-db \
> -e MYSQL_ROOT_PASSWORD=1809009 \
> -p 3306:3306 \
> --network localhost mysql:8.0
```
- `-d`: This flag is used to run the container in detached mode, meaning it runs in the background.

- `--name` mysql-db: This one is used to assign the name "mysql-db" to the Docker container.

- `-e` MYSQL_ROOT_PASSWORD=1809009: Sets the root password for MySQL to "1809009". This is the password we would use to connect to the MySQL server.

![mysql](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/4816d5d0-7352-4ba4-ae7a-2ef66a00db0e.png)