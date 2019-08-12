```sh
docker run -d --name some-clickhouse-server --ulimit nofile=262144:262144 -p 8123:8123 -p 9000:9000 yandex/clickhouse-server
docker run -d --name clickhouse-web -p 32080:80 spoonest/clickhouse-tabix-web-client
docker build -t testngx nginx/
docker run -d --name click-testngx -v $PWD/logs:/var/log/nginx -p 32880:80 testngx
while true; do curl localhost:8080; done
tail -f logs/access.log
docker run -d  --name nginx-clickhouse-adapter -v $PWD/logs:/logs -v $PWD/clickhousedb/config:/config mintance/nginx-clickhouse
```
```sql
CREATE DATABASE metrics
CREATE TABLE metrics.nginx (
    RemoteAddr String,
    RemoteUser String,
    TimeLocal DateTime,
    Date Date DEFAULT toDate(TimeLocal),
    Request String,
    RequestMethod String,
    Status Int32,
    BytesSent Int64,
    HttpReferer String,
    HttpUserAgent String
) ENGINE = MergeTree(Date, (Status, Date), 8192)
```

InfluxDB
```sh
docker run --name=influxdb -d -p 8086:8086 influxdb
```
Client for InfluxDB
```sh
docker run --rm --link=influxdb -it influxdb influx -host influxdb
```
Web Chronograf for InfluxDB
```sh
docker run -p 8888:8888 chronograf --influxdb-url=http://172.17.0.2:8086
```
```sh
docker build -t ha ./haproxy
docker run -d -p 8083:80 ha
```
Use this [github repo](https://github.com/shuLhan/haminer) to configure your custom golang haproxy logreader.

To build and run parser:
```sh
docker build -t parser ./logparser
docker run -d --name logparser parser
```
Install Grafana with clickhouse plugin:
```sh
docker run \
  -d \
  --name=grafana \
  -e "GF_INSTALL_PLUGINS=vertamedia-clickhouse-datasource" \
  -p 3000:3000 \
  grafana/grafana
```