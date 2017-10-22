## Log into Docker container as root
You can log into the Docker Image using the root user (ID = 0) instead of the
provided default user when you use the -u option. E.g.
```bash
docker exec -u 0 -it mycontainer bash
```
root (id = 0) is the default user within a container. The image developer can
create additional users. Those users are accessible by name. When passing a
numeric ID, the user does not have to exist in the container.
