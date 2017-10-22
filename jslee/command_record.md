# command_record analysis
**struct wrtier_data wd**
```
1495 struct writer_data {
1496     int             pid;
1497     int             pipefd;
1498     int             sock;
1499     int             nr_cpu;
1500     int             status;
1501     pthread_t           *writers;
1502     struct timespec         ts1, ts2;
1503     struct rusage           usage;
1504     struct uftrace_kernel_writer    kernel;
1505     struct uftrace_perf_writer  perf;
1506 };
```
**set pid and pipe file descriptor**
```
1777     wd.pid = pid;
1778     wd.pipefd = pfd[0];
```
- Q) I dont know why close pipefd[1] : `1779     close(pfd[1]);`
**`1781     setup_writers(&wd, opts);`**

