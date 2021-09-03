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
  

