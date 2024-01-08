# docker-networking-with-containers


## `Create an Internal Bridge Network`

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
---

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

---

## `Create an Nginx Container under the localhost Network`

```bash
docker run -d --name private-nginx -p 8080:80 --network localhost nginx
```
- `-d`: Runs the container in detached mode, meaning it runs in the background.
- `--name` private-nginx: Assigns the name "private-nginx" to the Docker container. This provides a human-readable identifier for the container.

- `-p` 8080:80: Maps port 8080 on the host to port 80 on the container.

- `--network` localhost: Connects the container to the Docker bridge network named "localhost." This will allows the Nginx container to communicate with other containers on the "localhost" network.

## `Test Connectivity`

Let's try with `curl` utility to get a response. From root ns.

```bash
curl localhost:8080
```
***Expected Output***
```
curl: (7) Failed to connect to localhost port 8080: Connection refused
```

Oppss! We are not getting anything back, as expected. Right?

## `Root Cause`
The `localhost` network is an internal network. It's not bound to any network interface on the Docker host.

## `Solution`
We can inspect the Docker container, grep the assigned private IP address, and then curl with that to get the result.

## `Inspect Docker Container to get IP`
```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' private-nginx
```
***Expected Output***
```
172.20.0.3
```
Now, we are good to curl again. From root ns.
```bash
curl 172.20.0.3:80
```
![bingo](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/a4e192f5-20b5-4c97-b8ee-b9b55830edc8.png)

Bingo! We are getting response from nginx server.
---