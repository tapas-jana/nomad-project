# nomad-project
#To install nomad on ubuntu
apt install unzip -y
Download zip file
wget https://releases.hashicorp.com/nomad/1.7.5/nomad_1.7.5_linux_amd64.zip
unzip nomad_1.7.5_linux_amd64.zip
mv nomad /usr/local/bin/
check nomad version
nomad -v
create a directory 
mkdir -p /etc/nomad.d
vi /etc/nomad.d/nomad.hcl
insert 
root@:~# cat /etc/nomad.d/nomad.hcl
data_dir  = "/opt/nomad"
bind_addr = "0.0.0.0"

advertise {
  http = "127.0.0.1"
  rpc  = "127.0.0.1"
  serf = "127.0.0.1"
}

server {
  enabled          = true
  bootstrap_expect = 1
}

client {
  enabled = true
}
plugin "raw_exec" {
  config {
    enabled = true
  }
}

ui {
  enabled = true
}

root@:~#
Run to start nomad manually
nomad agent -config=/etc/nomad.d/nomad.hcl
for systemd service 
root@:~# cat /etc/systemd/system/nomad.service
[Unit]
Description=Nomad
Documentation=https://www.nomadproject.io/
Requires=network-online.target
After=network-online.target

[Service]
ExecStart=/usr/local/bin/nomad agent -config=/etc/nomad.d
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
KillSignal=SIGINT
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target

root@:~#
systemctl daemon-reload
systemctl enable nomad.service
systemctl start nomad.service
That's all for nomad installation and setup
for nomad job's check .nomad files
to start job 
go to jamtestnet
root@:/opt/jamtestnet# ll
total 24
drwxr-xr-x 3 root root 4096 Feb 19 12:55 ./
drwxr-xr-x 7 root root 4096 Feb 18 04:39 ../
-rw-r--r-- 1 root root  576 Feb 19 09:43 jam-fib.nomad
-rw-r--r-- 1 root root  463 Feb 19 08:30 jam-setup.nomad
-rw-r--r-- 1 root root  875 Feb 19 10:06 jam-validator.nomad
drwxr-xr-x 8 root root 4096 Feb 19 12:42 release/
root@:/opt/jamtestnet#
nomad job run jam-setup.nomad # to setup environment
nomad job status to check status
nomad job run jam-validator.nomad 
Browse host-ip:4646
#for me
http://hostname:4646/ui/jobs
To start other job 
nomad job run jam-fib.nomad
To check status of job jam-validator
nomad job status jam-validator
get one allocation id and check status
nomad alloc status 1d7d1c09
to check logs 
nomad alloc logs 1d7d1c09
To stop a job and clean job
nomad job stop -purge jam-validator
nomad system gc

