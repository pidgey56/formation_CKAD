/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2025/12/15 10:39:13 [notice] 1#1: using the "epoll" event method
2025/12/15 10:39:13 [notice] 1#1: nginx/1.29.4
2025/12/15 10:39:13 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19) 
2025/12/15 10:39:13 [notice] 1#1: OS: Linux 6.6.87.2-microsoft-standard-WSL2
2025/12/15 10:39:13 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/12/15 10:39:13 [notice] 1#1: start worker processes
2025/12/15 10:39:13 [notice] 1#1: start worker process 29
2025/12/15 10:39:13 [notice] 1#1: start worker process 30
2025/12/15 10:39:13 [notice] 1#1: start worker process 31
2025/12/15 10:39:13 [notice] 1#1: start worker process 32
2025/12/15 10:39:13 [notice] 1#1: start worker process 33
2025/12/15 10:39:13 [notice] 1#1: start worker process 34
2025/12/15 10:39:13 [notice] 1#1: start worker process 35
2025/12/15 10:39:13 [notice] 1#1: start worker process 36
2025/12/15 10:39:13 [notice] 1#1: signal 3 (SIGQUIT) received, shutting down
2025/12/15 10:39:13 [notice] 31#31: gracefully shutting down
2025/12/15 10:39:13 [notice] 34#34: gracefully shutting down
2025/12/15 10:39:13 [notice] 30#30: gracefully shutting down
2025/12/15 10:39:13 [notice] 29#29: gracefully shutting down
2025/12/15 10:39:13 [notice] 31#31: exiting
2025/12/15 10:39:13 [notice] 34#34: exiting
2025/12/15 10:39:13 [notice] 36#36: gracefully shutting down
2025/12/15 10:39:13 [notice] 29#29: exiting
2025/12/15 10:39:13 [notice] 33#33: gracefully shutting down
2025/12/15 10:39:13 [notice] 33#33: exiting
2025/12/15 10:39:13 [notice] 32#32: gracefully shutting down
2025/12/15 10:39:13 [notice] 32#32: exiting
2025/12/15 10:39:13 [notice] 36#36: exiting
2025/12/15 10:39:13 [notice] 31#31: exit
2025/12/15 10:39:13 [notice] 34#34: exit
2025/12/15 10:39:13 [notice] 29#29: exit
2025/12/15 10:39:13 [notice] 33#33: exit
2025/12/15 10:39:13 [notice] 32#32: exit
2025/12/15 10:39:13 [notice] 36#36: exit
2025/12/15 10:39:13 [notice] 30#30: exiting
2025/12/15 10:39:13 [notice] 30#30: exit
2025/12/15 10:39:13 [notice] 35#35: gracefully shutting down
2025/12/15 10:39:13 [notice] 35#35: exiting
2025/12/15 10:39:13 [notice] 35#35: exit
2025/12/15 10:39:13 [notice] 1#1: signal 17 (SIGCHLD) received from 36
2025/12/15 10:39:13 [notice] 1#1: worker process 32 exited with code 0
2025/12/15 10:39:13 [notice] 1#1: worker process 36 exited with code 0
2025/12/15 10:39:13 [notice] 1#1: signal 29 (SIGIO) received
2025/12/15 10:39:13 [notice] 1#1: signal 17 (SIGCHLD) received from 32
2025/12/15 10:39:13 [notice] 1#1: signal 17 (SIGCHLD) received from 35
2025/12/15 10:39:13 [notice] 1#1: worker process 29 exited with code 0
2025/12/15 10:39:13 [notice] 1#1: worker process 30 exited with code 0
2025/12/15 10:39:13 [notice] 1#1: worker process 31 exited with code 0
2025/12/15 10:39:13 [notice] 1#1: worker process 33 exited with code 0
2025/12/15 10:39:13 [notice] 1#1: worker process 34 exited with code 0
2025/12/15 10:39:13 [notice] 1#1: worker process 35 exited with code 0
2025/12/15 10:39:13 [notice] 1#1: exit
