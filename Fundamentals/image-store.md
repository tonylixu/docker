### Where does Docker store its images?
Normally under "/var/lib/docker" directory, but the contents very depending on
the driver Docker is using. You can check the driver by the following command:
```bash
$ docker info
Containers: 32
 Running: 30
 Paused: 0
 Stopped: 2
Images: 41
Server Version: 17.06.1-ce
Storage Driver: overlay
 Backing Filesystem: extfs
 Supports d_type: true
```

In my case, I use the "overlay" driver.

### How the "overlay" driver works
