# Docker Containers Cheat Sheet¶
---

## What's a Docker Container?¶

A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

## Containers¶

Your basic isolated Docker process. Containers are to Virtual Machines as threads are to processes. Or you can think of them as chroots on steroids.

## Lifecycle¶

-  `docker create` creates a container but does not start it.
-  `docker rename` allows the container to be renamed.
-  `docker run` creates and starts a container in one operation.
-  `docker rm` deletes a container.
-  `docker update` updates a container's resource limits.

Normally if you run a container without options it will start and stop immediately, if you want keep it running you can use the command, `docker run -td container_id` this will use the option `-t` that will allocate a pseudo-TTY session and `-d` that will detach automatically the container (run container in background and print container ID).

If you want a transient container, `docker run --rm` will remove the container after it stops.

If you want to map a directory on the host to a docker container, `docker run -v $HOSTDIR:$DOCKERDIR`. Also see **Volumes**.

If you want to remove also the volumes associated with the container, the deletion of the container must include the `-v` switch like in docker rm `-v`.

There's also a logging driver available for individual containers in docker 1.10. To run docker with a custom log driver (i.e., to syslog), use `docker run --log-driver=syslog`.

Another useful option is `docker run --name yourname docker_image` because when you specify the `--name` inside the run command this will allow you to start and stop a container by calling it with the name the you specified when you created it.

## Starting and Stopping¶

- `docker start` starts a container so it is running.
- `docker stop` stops a running container.
- `docker restart` stops and starts a container.
- `docker pause` pauses a running container, "freezing" it in place.
- `docker unpause` will unpause a running container.
- `docker wait` blocks until running container stops.
- `docker kill` sends a SIGKILL to a running container.
- `docker attach` will connect to a running container.

If you want to detach from a running container, use `Ctrl + p`, `Ctrl + q`. If you want to integrate a container with a host process manager, start the daemon with `-r=false` then use docker start `-a`.

If you want to expose container ports through the host, see the exposing ports section.

Restart policies on crashed docker instances are covered here.

## CPU Constraints¶

You can limit CPU, either using a percentage of all CPUs, or by using specific cores.

For example, you can tell the cpu-shares setting. The setting is a bit strange -- 1024 means 100% of the CPU, so if you want the container to take 50% of all CPU cores, you should specify 512. See https://goldmann.pl/blog/2014/09/11/resource-management-in-docker/#_cpu for more:

```bash
docker run -it -c 512 agileek/cpuset-test
```
You can also only use some CPU cores using cpuset-cpus. See https://agileek.github.io/docker/2014/08/06/docker-cpuset/ for details and some nice videos:
```bash
docker run -it --cpuset-cpus=0,4,6 agileek/cpuset-test
```
Note that Docker can still see all of the CPUs inside the container -- it just isn't using all of them. See https://github.com/docker/docker/issues/20770 for more details.

## Memory Constraints¶

You can also set memory constraints on Docker:

```bash
docker run -it -m 300M ubuntu:14.04 /bin/bash
```
## Capabilities¶

Linux capabilities can be set by using cap-add and cap-drop. See https://docs.docker.com/engine/reference/run/#/runtime-privilege-and-linux-capabilities for details. This should be used for greater security.

To mount a FUSE based filesystem, you need to combine both `--cap-add` and `--device`:
```bash
docker run --rm -it --cap-add SYS_ADMIN --device /dev/fuse sshfs
```
Give access to a single device:
```bash
docker run -it --device=/dev/ttyUSB0 debian bash
```
Give access to all devices:
```bash
docker run -it --privileged -v /dev/bus/usb:/dev/bus/usb debian bash
```
More info about privileged containers here.

## Info¶

- `docker logs` gets logs from container. (You can use a custom log driver, but logs is only available for json-file and journald in 1.10).
- `docker inspect` looks at all the info on a container (including IP address).
- `docker events` gets events from container.
- `docker port` shows public facing port of container.
- `docker top` shows running processes in container.
- `docker stats` shows containers' resource usage statistics.
- `docker diff` shows changed files in the container's FS.
- `docker ps` shows running containers.

`docker ps -a` shows running and stopped containers.

`docker stats --all` shows a list of all containers, default shows just running.

## Import / Export¶

    docker cp copies files or folders between a container and the local filesystem.
    docker export turns container filesystem into tarball archive stream to STDOUT.

## Executing Commands¶

- `docker exec` to execute a command in container.

To enter a running container, attach a new shell process to a running container called foo, use: `docker exec -it foo /bin/bash`.

## Credit¶

Thanks to @wsargent for creating this cheat sheet.
