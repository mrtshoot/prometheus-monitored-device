# prometheus-monitored-device
sample configuration for monitored device

we need some basic docker image to provide prometheus server scraper.to do this we need two basic exporter one of them is node_exporter and other one is prometheus_speedtest for ping latency and speed test of bandwidth.

## Step1
clone this repo with following command

```
git clone https://github.com/mrtshoot/prometheus-monitored-device.git
```

## Step2
Create needed directories and files
```
cd prometheus-monitored-device;mkdir -p proxy/config/conf.d
```
and then needed files
```
touch proxy/config/conf.d/prometheus_const.conf; touch proxy/config/conf.d/local.conf
```
 
## Step3
Add following content to local.conf

```
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html index.htm;

  location /node_metrics {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://node_exporter:9100/metrics;
  }

  location /probe {
    include /etc/nginx/conf.d/prometheus_const.conf;
    proxy_pass http://prom_speedtest:9516/probe;
  }
}
```

## Step4
Add following content to prometheus_const.conf

```
allow [SingleIP/Subnet];
deny  all;
proxy_set_header Host $host;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Real-IP $remote_addr;
```

because of security you do not expose your prometheus exporter api you need access allowed addresses

## Step5
open Web Port for Proxy

```
sudo firewall-cmd --add-port=8085/tcp --permanent && sudo firewall-cmd --reload
```

## Step6
Up and Running Your Compose File

```
cp docker-compose.yml.sample docker-compose.yml;docker-compose up -d
```

# Nginx Monitoring

## How to Monitoring Nginx Request and Connection on Specific Time Range?

Only execute following command to run Nginx Exporter

```
docker run -p 9113:9113 nginx/nginx-prometheus-exporter -nginx.scrape.uri=https://mynginxdomain.com/nginx_status -nginx.retries=10 -web.telemetry-path=/nginx_prom
```

### Author
### Mrtshoot
