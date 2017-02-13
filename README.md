# Introduction
A simple &amp; high efficient Shell script for synchronizing log using NFS.
`sync_log` tracks the log file(i.e. `<src_file>`), save it as slice files under a temporary directory(i.e `<tmp_dir>`) and move slice files to the destination directory(i.e. `<dst_dir>`) at last every `<check_interval_sec>` seconds(defaults to 20 seconds).
`merge_log` merges slice files into `<dst_file_name>`.

# Platform
* CentOS 6.6
* macOS 10.12
* Other Linux(NOT TESTED)

# Feature
* Simple: easy to deploy, only 1-2 script files, no need to run `yum install xxx`;
* High efficient: script files use Linux native commands like `mv`, `tail`, `grep`, `pgrep`, `cat`, `kill`, etc. only;
* Re-transport on network failure: the slice files will be deleted only they are successfully tranported otherwise they be transported again and again;
* Lossless: logs will not be lost even under QPS 100k. (Please notice that logs will be *repeated* about 4% under QPS 100k while the number <= 2/1000 under QPS 14k);

# Tutorial
## VM-1(IP: 192.168.3.10)
```
# Mount points. Notice that `/mnt/nfs` is a remote directory while `/` is a local one:
df -h
# Filesystem                          Size  Used Avail Use% Mounted on
# /dev/sda1                            20G  4.1G   15G  22% /
# 192.168.3.20:/nfs/192.168.3.10      2.8T  1.6T  1.3T  56% /mnt/nfs

# make <tmp_dir> and <dst_dir>
mkdir -p /tmp/nginx/access.log.d /mnt/nfs/nginx/access.log.d

# start sync_log
sync_log start /var/log/nginx/access.log /tmp/nginx/access.log.d /mnt/nfs/nginx/access.log.d 20

# stop sync_log
# sync_log stop /tmp/nginx/access.log.d

# help
# sync_log -h
```
## VM-2(IP: 192.168.3.20)
```
# <dst_dir> hirarchy:
tree /nfs/192.168.3.10
# 192.168.3.10
# └── nginx
#     └── access.log.d
#         ├── 0213T173610
#         ├── 0213T173630
#         ├── 0213T173650
#         └── access.log

# start merge_log
merge_log /nfs nginx/access.log.d access.log --ips=192.168.3.10

# stop merge_log
# Ctrl + C

# help
# merge_log -h
```

# Any Question
lslxdx#163.com

