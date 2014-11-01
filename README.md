# Journalist

Journalist runs `logstash` to aggregate logs, in conjunction with [`clay/scribe`](https://github.com/claydotio/scribe-public)
which ships logs.

#### Pre-build

Generate tls cert (may have to build `lc-tlscert.go` - https://golang.org/)

```bash
./lc-tlscert
```

Create a `./certs` directory and move the generated certs to:

```
certs/logstash-forwarder.crt
certs/logstash-forwarder.key
```

#### Build

```bash
docker build -t journalist .
```

#### Run

```bash
docker run \
    --restart on-failure \
    -v /data/elasticsearch:/data/elasticsearch \
    -p 5043:5043 \
    -p 514:514 \
    -p 9292:9292 \
    -p 9200:9200 \
    -p 9300:9300 \
    --name journalist \
    -d \
    -t journalist
```

Now you can checkout your logs here: http://1.2.3.4:9292/index.html

![logstash](logstash_screenshot.png)

#### Scribe

Once you set up the master journalist server, you can start shipping logs to it
via [`clay/scribe`](https://github.com/claydotio/scribe-public)

Copy `./certs` into your `clay/scribe` repo directory  
Edit `run.sh`

```bash
docker build -t scribe .
```

```bash
docker run \
    --restart always \
    -v /var/log/app:/var/log/app \
    -e LOGSTASH_SERVER=1.2.3.4:5043 \
    --name scribe \
    -d \
    -t scribe
```

See Scribe docs for optional `logrotate` setup information
