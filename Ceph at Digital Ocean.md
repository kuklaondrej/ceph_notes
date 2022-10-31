
38 clusters, >54PB of raw Ceph storage, ~21500 OSD
	- 1,42PB per cluster, 565 OSDs per cluster
- running in custom **container images**

- block storage = all flash
	- RBD (RADOS Block Device) - great fit with KVM QEMU -> looks like normal drive

- object storage = HDD and flash -> flash for index nodes 

#### Monitoring
---
- custom prometheus [ceph_exporter](https://github.com/digitalocean/ceph_exporter)
- store-exporter and node_exporter gathers data from the admin socket of each deamon on all hosts

#### RGW related stuff
---
- dynamic resharding **blocks writes** for some time (write requests will be stuck on RGW -> takes threads) - problematic on large buckets
- automatic shard removal can be problematic on large buckets