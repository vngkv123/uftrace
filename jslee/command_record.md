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

**important routine**
- `1781     setup_writers(&wd, opts);`
- `1584 static void start_tracing(struct writer_data *wd, struct opts *opts, int ready_fd)`
- `1622         pthread_create(&wd->writers[i], NULL, writer_thread, warg);`
- ` 565 void *writer_thread(void *arg)`


**show call graph -> command_replay()**
- `1011             ret = print_graph_rstack(&handle, task, opts);` -> show call graph

**struct sym**
```
 27 struct sym {
 28     uint64_t addr;
 29     unsigned size;
 30     enum symtype type;
 31     char *name;
 32 };
 ```
 - -> sym->name has function name

**write symbol file**
- in `uilts/symbol.c`
```
1185 do_it:
1186     /* dynamic symbols */
1187     for (i = 0; i < dtab->nr_sym; i++)
1188         fprintf(fp, "%016"PRIx64" %c %s\n", dtab->sym_names[i]->addr - offset,
1189                (char) dtab->sym_names[i]->type, dtab->sym_names[i]->name);
```
