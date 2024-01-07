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

## `Create another Container under the localhost Network`

Here we will create a Ubuntu image-based container under the bridge network, then we will connect to the localhost container, and finally we will ping the `mysql-db` container.

```bash
docker run -d --name host-vm ubuntu
docker network connect localhost host-vm
docker container start -ia host-vm
```
Now, we need to install some prerequisites to use some IP utilities.

```bash
apt update
apt install iproute2
apt install net-tools
apt install iputils-ping
```

## `Test Connectivity`

No worries, we are good to ping now.
```bash
ping mysql-db -c 5
```

![ping](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/f369fda1-7b8b-4876-8e20-cc529caec073.png)

Yahoo! We have established communication between two containers under the same bridge network.