# Prometheus with node-exporter and Speed Test and  Grafana Visualization

## Getting Started

Prometheus is simple and open source monitoring project.its started on sound cloud since 2012 and join to Cloud Native Computing on 2016.
prometheus have many online developer and big community on the World.
if you need more explaination got to https://prometheus.io official website.

Prometheus system is pulling from own Agent.
agents or exporter
for example:
for node monitoring such as Disk, CPU, RAM and etc we need node-exporter 
for mysql or MariaDB monitoring we need mysql-exporter
...

Prometheus has Alert Managing System for Threshold.

we have some simple docker compose for Prometheus, Grafana and Node Exporter Monitoring System Resources.

### Prerequisites
What things you need to install

```
1. Install Docker
2. Install docker-compose
```

### Installing

Create Prometheus Data Directory

```
cd prometheus-docker;mkdir data
```

Create Nginx Reversed Proxy Directories for Configurations and Logs on prometheus-docker directory

```
mkdir -p proxy/config/conf.d;mkdir -p proxy/logs
```

Create Your Reverse Proxy Configuration File

```
touch proxy/config/conf.d/local.conf
```

Add your sample Configuration Proxy to Upstream Web Server on local.conf

```
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html index.htm;

  location / {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://grafana:3000/;
  }  

  location /node_metrics {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://node_exporter:9100/metrics;
  }

  location /alerts {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prometheus:9090/alerts;
  }

  location /status {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prometheus:9090/status;
  }

  location /flags {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prometheus:9090/flags;
  }

  location /config {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prometheus:9090/config;
  }

  location /rules {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prometheus:9090/rules;
  }

  location /service-discovery {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prometheus:9090/service-discovery;
  }

  location /targets {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prometheus:9090/targets;
  }

  location /probe {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prom_speedtest:9516/probe;
  }
}
```

Create Proxy Consistent File
```
touch proxy/config/conf.d/prometheus_const.conf
```

Add your Sample Proxy Configuration for include it on local.conf file.
```
allow [IP/SUBNET];
deny  [IP/SUBNET];
proxy_set_header Host $host;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Real-IP $remote_addr;
```

Copy sample Prometheus Configuration file and Add your jobs and node-exporter informations on it.

```
cp prometheus.yml.sample prometheus.yml
```

Create your Docker Compose File and set Data Directory of prometheus


```
cp docker-compose.yml.sample docker-compose.yml
```

Up and Running your System

```
docker-compose up -d
```

#### Notice

Prometheus is Time Sensitive.check and Sync your Date and Time with your Current Location and Browser otherwise you may see exception on prometheus queries.
on Linux you can follow this Structure

```
date -R
```

after underestand structure you can set time with following example command

```
date -s "12 Feb 2020 20:52:19 +0330"
```

## Authors

* **Hossein Ghorbani(MrTshoot)** - *Follow me* - [Hossein Ghorbani](https://github.com/mrtshoot)





