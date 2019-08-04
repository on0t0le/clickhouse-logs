docker run -d --name some-clickhouse-server --ulimit nofile=262144:262144 -p 8123:8123 -p 9000:9000 yandex/clickhouse-server
docker run -d --name clickhouse-web -p 32080:80 spoonest/clickhouse-tabix-web-client
docker build -t testngx nginx/
docker run -d --name testngx -v $PWD/logs:/var/log/nginx -p 8080:80 testngx
while true; do curl localhost:8080; done
tail -f logs/access.log
docker run -d  --name nginx-clickhouse-adapter -v $PWD/logs:/logs -v $PWD/clickhousedb/config:/config mintance/nginx-clickhouse

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