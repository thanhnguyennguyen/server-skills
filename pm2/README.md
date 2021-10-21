### PM2 working notes
- Fork mode

Take the fork mode as a basic process spawning. This allows to change the exec_interpreter, so that you can run a php or a python server with pm2. Yes, the exec_interpreter is the "command" used to start the child process. By default, pm2 will use node so that pm2 start server.js

- Cluster mode

The cluster will only work with node as it's exec_interpreter because it will access to the nodejs cluster module (eg: isMaster, fork methods etc.). This is great for zero-configuration process management because the process will automatically be forked in multiple instances. For example pm2 start -i 4 server.js will launch 4 instances of server.js and let the cluster module handle load balancing.

- [Reload pm2 without downtime ](https://blog.haposoft.com/pm2-cluster-mode-and-zero-downtime-restarts/)
