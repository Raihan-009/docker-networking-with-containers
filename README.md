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