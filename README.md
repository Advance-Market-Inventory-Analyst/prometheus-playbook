# prometheus-playbook
prometheus-playbook

Let’s connect to the AWS EC2 instance where we are going to install Prometheus. prometheus.pem is the key that was generated previously.

ssh -i prometheus.pem ubuntu@ec2-3-17-28.53.us-east-2.compute.amazonaws.com

create a directory to host Prometheus configuration and another one to host its data.

ubuntu@ip-172-31-89-209:~$ sudo useradd --no-create-home prometheus
ubuntu@ip-172-31-89-209:~$ sudo mkdir /etc/prometheus
output
ubuntu@ip-172-31-89-209:/etc/prometheus$

ubuntu@ip-172-31-89-209:~$ sudo mkdir /var/lib/prometheus
output
ubuntu@ip-172-31-89-209:/var/lib/prometheus$

Now we need to install Prometheus.

ubuntu@ip-172-31-89-209:~$ sudo wget https://github.com/prometheus/prometheus/releases/download/v2.19.0/prometheus-2.19.0.linux-amd64.tar.gz

output
Saving to: ‘prometheus-2.19.0.linux-amd64.tar.gz’

prometheus-2.19.0.linux-amd64.tar.gz                100%[=================================================================================================================>]  61.19M  87.3MB/s    in 0.7s    

2021-09-03 19:04:00 (87.3 MB/s) - ‘prometheus-2.19.0.linux-amd64.tar.gz’ saved [64161216/64161216]


ubuntu@ip-172-31-89-209:/$ sudo tar xvfz prometheus-2.19.0.linux-amd64.tar.gz

output

prometheus-2.19.0.linux-amd64/
prometheus-2.19.0.linux-amd64/consoles/
prometheus-2.19.0.linux-amd64/consoles/prometheus.html
prometheus-2.19.0.linux-amd64/consoles/node-overview.html
prometheus-2.19.0.linux-amd64/consoles/node-disk.html
prometheus-2.19.0.linux-amd64/consoles/index.html.example
prometheus-2.19.0.linux-amd64/consoles/prometheus-overview.html
prometheus-2.19.0.linux-amd64/consoles/node.html
prometheus-2.19.0.linux-amd64/consoles/node-cpu.html
prometheus-2.19.0.linux-amd64/tsdb
prometheus-2.19.0.linux-amd64/NOTICE
prometheus-2.19.0.linux-amd64/prometheus
prometheus-2.19.0.linux-amd64/LICENSE
prometheus-2.19.0.linux-amd64/prometheus.yml
prometheus-2.19.0.linux-amd64/console_libraries/
prometheus-2.19.0.linux-amd64/console_libraries/menu.lib
prometheus-2.19.0.linux-amd64/console_libraries/prom.lib
prometheus-2.19.0.linux-amd64/promtool

ubuntu@ip-172-31-89-209:/$ cd /prometheus-2.19.0.linux-amd64

output

ubuntu@ip-172-31-89-209:/prometheus-2.19.0.linux-amd64$ ./prometheus --version

output

prometheus, version 2.19.0 (branch: HEAD, revision: 5d7e3e970602c755855340cb190a972cebdd2ebf)
  build user:       root@d4cf5c7e268d
  build date:       20200609-10:29:59
  go version:       go1.14.4
  
  
 Prometheus is an open-source monitoring system which is very lightweight and has a good alerting mechanism.

Install and Configure Prometheus
This guide explains how to install and configure the latest Prometheus on a Linux VM.

If you would like to install Prometheus on a Kubernetes cluster, please see the Prometheus on kubernetes guide.

Before You Begin

Ensure that you have sudo access to the Linux server because the commands used in this guide require elevated privileges.
The server has access to the internet for downloading the Prometheus binary.
Most importantly, firewall rules opened for accessing Prometheus port 9090 on the server.
Setup Prometheus Binaries

Step 1: Update the yum package repositories.

sudo yum update -y
Step 2: Go to the official Prometheus downloads page and get the latest download link for the Linux binary.

prometheus linux download link

Step 3: Download the source using curl, untar it, and rename the extracted folder to prometheus-files.

curl -LO url -LO https://github.com/prometheus/prometheus/releases/download/v2.22.0/prometheus-2.22.0.linux-amd64.tar.gz
tar -xvf prometheus-2.22.0.linux-amd64.tar.gz
mv prometheus-2.22.0.linux-amd64 prometheus-files


Step 4: Create a Prometheus user, required directories, and make Prometheus the user as the owner of those directories.

sudo useradd --no-create-home --shell /bin/false prometheus

sudo mkdir /etc/prometheus

sudo mkdir /var/lib/prometheus

sudo chown prometheus:prometheus /etc/prometheus

sudo chown prometheus:prometheus /var/lib/prometheus


Step 5: Copy prometheus and promtool binary from prometheus-files folder to /usr/local/bin and change the ownership to prometheus user.

sudo cp prometheus-files/prometheus /usr/local/bin/

sudo cp prometheus-files/promtool /usr/local/bin/

sudo chown prometheus:prometheus /usr/local/bin/prometheus

sudo chown prometheus:prometheus /usr/local/bin/promtool

Step 6: Move the consoles and console_libraries directories from prometheus-files to /etc/prometheus folder and change the ownership to prometheus user.

sudo cp -r prometheus-files/consoles /etc/prometheus

sudo cp -r prometheus-files/console_libraries /etc/prometheus

sudo chown -R prometheus:prometheus /etc/prometheus/consoles

sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries


Setup Prometheus Configuration


All the prometheus configurations should be present in /etc/prometheus/prometheus.yml file.

Step 1: Create the prometheus.yml file.

sudo vi /etc/prometheus/prometheus.yml

Step 2: Copy the following contents to the prometheus.yml file.

global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
      - 
Step 3: Change the ownership of the file to prometheus user.

sudo chown prometheus:prometheus /etc/prometheus/prometheus.yml

Setup Prometheus Service File

Step 1: Create a prometheus service file.

sudo vi /etc/systemd/system/prometheus.service

Step 2: Copy the following content to the file.

[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target

Step 3: Reload the systemd service to register the prometheus service and start the prometheus service.

sudo systemctl daemon-reload

sudo systemctl start prometheus

Check the prometheus service status using the following command.

sudo systemctl status prometheus

The status should show the active state as shown below.

prometheus status

Access Prometheus Web UI

Now you will be able to access the prometheus UI on 9090 port of the prometheus server.

http://<prometheus-ip>:9090/graph
  
You should be able to see the following UI as shown below.

prometheus web UI
  
You can use the prometheus query tab to query the available metrics as shown in the gig below.

querying metrics from prometheus UI
  
Right now, we have just configured the Prometheus server. You need to register the target in the prometheus.yml file to get the metrics from the source systems.

For example, if you want to monitor ten servers, the IP address of these servers should be added as a target in the Prometheus configuration to scrape the metrics.

The server should have Node Exporter installed to collect all the system metrics and make it available for Prometheus to scrap it.

Follow this detailed Prometheus Node Exporter Guide to setup node exporter and registering it to the Prometheus server. 
  

