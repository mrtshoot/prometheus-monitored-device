# prometheus-monitored-device
sample configuration for monitored device

we need some basic docker image to provide prometheus server scraper.to do this we need two basic exporter one of them is node_exporter and other one is prometheus_speedtest for ping latency and speed test of bandwidth.

## Step1
clone this repo with following command

```
git clone https://github.com/mrtshoot/prometheus-monitored-device.git;cd prometheus-monitored-device
```

## Step2
Create needed directories and files
```
mkdir -p proxy/config/conf.d
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

# Real Host Monitoring
to real host os monitoring you should install node exporter on your real host os.

## Step1
Download last release node_exporter base on your os architecture
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz
```

## Step2
Extract file and rename it
```
tar -xzvf node_exporter-1.0.1.linux-amd64.tar.gz;mv node_exporter-1.0.1.linux-amd64.tar.gz node_exporter
```

# Step3
move node_exporter to your binary linux location and set right execute permission.
```
mv node_exporter /usr/local/bin/node_exporter;chmod -R +x /usr/local/bin/node_exporter
```

# Step4
Create node exporter service daemon under /etc/systemd/system directory.
```
vim /etc/systemd/system/node_exporter.service
```

Copy following content on node_exporter.service
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
ExecStart=/usr/local/bin/node_exporter/node_exporter

[Install]
WantedBy=default.target
```
## Step5
Reload Daemon Service and Enable and Start node_exporter service
```
sudo systemctl daemon-reload;sudo systemctl enable node_exporter.service;sudo systemctl start node_exporter.service;sudo systemctl status node_exporter.service -l
```

## Step6
Finaly you need add your host on prometheus yaml file on prometheus server.


# Nginx Monitoring

## How to Monitoring Nginx Request and Connection on Specific Time Range?

Only execute following command to run Nginx Exporter

```
docker run -p 9113:9113 nginx/nginx-prometheus-exporter -nginx.scrape.uri=https://mynginxdomain.com/nginx_status -nginx.retries=10 -web.telemetry-path=/nginx_prom
```

### Author
### Mrtshoot
