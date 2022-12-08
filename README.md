# RGW

#### Accessing files

---

- S3 API
- Swift API - connection with OpenStack

! Mixing APIs can lead to file permission problems. Therefore it isn't recomended. !
> Both wrappers over [librados](https://docs.ceph.com/en/latest/rados/api/librados-intro/)

**optimal GW to ODS ratio -> 1:50 - 1:100 OSDs**

#### RGW Performance

---
> based on [Red Hat Ceph Storage RGW deployment strategies and sizing guidance](https://www.redhat.com/en/blog/red-hat-ceph-storage-rgw-deployment-strategies-and-sizing-guidance)

- colocating multiple RGW instances on the OSD node delivers most performance

- write workloads
  - CPU core count matters -> 4 CPU Core per RGW instance seems optimal
- read workloads
  - increasing CPU core count ≠ increased read performance
  - erasure coding doesn't need to decode chunks while reading -> easy on CPU

#### Service Pools

---

- root - zone group and zone configuration
- control
- garbage collection
- logs - logging and intent logging
- user management - S3 UIDs, Swift UIDs, access keys, user emails, quotas/usage

#### Data Placement Pools

---

> Placement policies allow having different HW configuration for different performance characteristics

- bucket index - stores an index of objects - ***SSD/NVMe and sharding***
- bucket - stores data - ***Use erasure coding***
- extras - stores other data, such as multipart uploads - ***SSD/NVMe***

- dynamic resharding - is able to DoS a cluster on very large bucktes
  - the process of resharding that can take hours on huge buckets **blocks writes** for the bucket on the Rados gateway and they will never timeout, but the client will so he will reconnect and try it again -> eating threads like crazy
  - limit the max size of a bucket or disable resharding
  - automatic shard removal can be problematic on large buckets
- manual resharding - aprox. one shard per 100k objects

#### TBA

---

- performance tuning
- RGW at Cern

## Ceph at DigitalOcean

---

- 38 clusters, >54PB of raw Ceph storage, ~21500 OSD
  - 1,42PB per cluster, 565 OSDs per cluster

- running in custom **container images**

- block storage = all flash
  - RBD (RADOS Block Device) - great fit with KVM QEMU -> looks like normal drive

- object storage = HDD and flash -> flash for index nodes

#### Monitoring

---

- custom prometheus [ceph_exporter](https://github.com/digitalocean/ceph_exporter)
- node_exporter
- store-exporter gathers data from the admin socket of each deamon on all hosts

### Useful sources

---

- [Red Hat Ceph Storage RGW deployment strategies and sizing guidance](https://www.redhat.com/en/blog/red-hat-ceph-storage-rgw-deployment-strategies-and-sizing-guidance)
- [Optimizing Ceph Object Storage For Production in Multisite Clouds](https://www.redhat.com/files/summit/session-assets/2018/Optimize-Ceph-object-storage-for-production-in-multisite-clouds.pdf)
- [Ceph Tech Talk: Ceph at DigitalOcean](https://www.slideshare.net/Inktank_Ceph/ceph-tech-talk-ceph-at-digitalocean)
- [Building CDN with Ceph object storage](https://www.youtube.com/watch?v=I-DQ7k_LiOQ)
- [Past, present and future of the storage in the CERN private cloud](https://www.youtube.com/watch?v=Ik9BMkFvsoI)
- [Optimizing RGW Object Storage Mixed Media through Storage Classes and Lua Scripting](https://www.youtube.com/watch?v=w91e0EjWD6E)