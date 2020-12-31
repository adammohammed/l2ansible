# Automation Base Infrastructure

This repo will be used as the base for some IOT projects.

For now this repo will stand up and configure influxdb and mqtt, so that my IOT devices can log and query for information.


## InfluxDB

The setup of influxdb through this repo expects that there is a docker volume which persists the data for the db. This volume can be created locally by running the container, and doing the initial setup (e.g. Creating a username and password).

To migrate the local docker volume to the target host, you'll want to back it up first, then extract it on the remote host.

1. Create the influxdb volume
``` shell
docker run -e INFLUXD_STORE=bolt -e INFLUXD_BOLT_PATH=/var/lib/influxdb/influxd.bolt -e INFLUXD_ENGINE_PATH=/var/lib/influxdb/engine -v local-influx:/var/lib/influxdb -p 8086:8086 quay.io/influxdb/influxdb:v2.0.3
```

2. Go to the UI at http://localhost:8086, and set up your instance.

3. Shutdown your local instance

``` shell
docker rm -f <influx_container_id>
```

4. Start a container up to do the backup

``` shell
docker run -v local-influx:/var/lib/influxdb -v $(pwd):/backup ubuntu tar cvzf /backup/backup.tar.gz /var/lib/influxdb
```

5. Transfer the file to the remote host

6. Create a docker volume on the remote host

``` shell
docker volume create remote-influx
```

7. Extract the contents of the tarball to the volume

``` shell
docker run -v remote-influx:/var/lib/influxdb -v $(pwd):/backup ubuntu tar xvzf /backup/backup.tar.gz
```


8. Finally, run the playbook to setup the container and network.

``` shell
ansible-playbook site.yml -K
```
