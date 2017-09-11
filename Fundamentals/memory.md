### Memory Default
By default, a Docker container has no resource constraints and can use as much
of a given resource as the hosts's kernel scheduler will allow. This sometimes
can be very dangerous, you don't wnat a running container to consume too much
of the host's memory or CPU.

If the Linux kernel detects that there is not enough memory to perform
important system functions, it will starts killing processes to free up some
memory, in the system log files, you will see "00ME" or "Out of Memory
Exception". What if the kernel decides to kill a production container process?

### OOM priority
Docker attempts to mitigate the "00ME" risk by adjusting the OOM priority on
the Docker daemon so that it will be less likely to be killed. But the OOM
priority on Docker containers are not adjusted. This means Docker containers
will more likely be killed than Docker daemon. Even with that being said, you
should not try to circumvent these safeguards by playing around with
"--oom-score-adj" and "--oom-disable-kill", it is better to get one container
killed instead of the Docker daemon. The right way is to set the memory limit
for the container.

