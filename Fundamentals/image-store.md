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
Overlay FS layers two directories on a single Linux host and presents them as a
single directory. These directories are called layers and the unification
process is referred to a "union mount". Overlay FS refers to the lower
directory as lowerdir and the upper directory as a upperdir. The unified view
is exposed through its own directory called "merged".

The "image" layer is the "lowerdir" and the "container" layer is the
"upperdir". The unified view is exposed through a directory called "merged"
which is effectively the contaners mount point.

The "overlay" driver only works with two layers. This means that multi-layered
images cannot be implemented as multiple OverlayFS layers. Instead, each image
layer is implemented as its own directory under "/var/lib/docker/overlay".
Hard links are then used as a space-efficient way to reference data shared with
lower layers.

### Create container
To create a container, the "overlay" driver combines the directories
representing the image's top layer plus a new directory for the container. The
image’s top layer is the lowerdir in the overlay and is read-only. The new
directory for the container is the upperdir and is writable.

This example shows you how a newly built images contains 8 layers:

```bash
$ cat
/var/lib/docker/image/overlay/imagedb/content/sha256/7d8615604a7310835739e52f8566aaf6f70bbb889d76fc9f675479c501cc0307
| python -mjson.tool

...
"os": "linux",
    "rootfs": {
        "diff_ids": [
            "sha256:826fc2344fbbc40cf9f2714c831a0d3ff88596e471f71c33b1055f3913d829d4",
            "sha256:cf195c989cebc6d8b6329967b2f012adc61fcc057eb50c98ea5f8cf0a5a3bee9",
            "sha256:45dee3415f4256915c49347acd06e55c3ebad1571abaf18556110dccc5de2e21",
            "sha256:05e7ab001e8ad7a0d236b0ccf02e5a1545adfcd16fae515d4dfa60057dbacdf0",
            "sha256:f668bc0d79c10da6e11623d3b129c6c2e8115614aebc270fef587a9e53b66289",
            "sha256:a82b2abc1bdc47cadef7c35fec86898a54fcf21ae00df9c456cc32af91ff4d5e",
            "sha256:f014d4a9f4ad2f996e581efde9ac0ad090692c3af6130dff03cb3a748f6560b6",
            "sha256:e0a1803e28ee977d9c82ffff7934b9b35960b4194baccc2f9c7c6351e41a31f7"
        ],
        "type": "layers"}
```

Each image layer has its own directory within "/var/lib/docker/overlay/", which
contains its contents.
```bash
$ ls -l /var/lib/docker/overlay
...
drwx------   3 root root  4096 Sep 10 14:58
6525a2b96afbe70bfa1a9f87207f3e4365060f0988a9b1268c9b0d1e0f6f5449/
drwx------   3 root root  4096 Sep 10 14:58
f46c5e09c86abfc32a2449527a272dea019a473aa8945c1f8b53eae11e9a04f6/
drwx------   3 root root  4096 Sep 10 14:58
da551aceebcf811b294d60bed475e06b39c73bb60cfbf0844a689d57db093c66/
drwx------   3 root root  4096 Sep 10 14:57
171cfe7a79ac1ae5fd37cf08a3a0c93d754edf52db057835233c5e6ed313b16f/
drwx------   3 root root  4096 Sep 10 14:57
30f84ec35cceed3881cae1b304327056a5445d1b1bce8571f8a97b0cecbadaf7/
drwx------   3 root root  4096 Sep 10 14:57
430897eeb09b763318979fdeb78945e92cc73f4c9d16a158c8fee252a76171f0/
drwx------   3 root root  4096 Sep 10 14:57
d6af9b5e934394864ae759756514f72efcda52ab3d39c86010160e0e35a800a6/
drwx------   3 root root  4096 Sep 10 14:56
31da096dd0f2e231ff2f7f18d6e96cd9a71178f3955643f9d0f0bf3cf76f212c/
```
Notice that the image layer IDs do not correspond to the directory IDs.
