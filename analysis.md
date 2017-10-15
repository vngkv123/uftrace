# debug with gdb
**quit strange in asm code**
- start_pager, start_command is consolidated...
```
gef➤  p &start_pager
$1 = (void (*)(void)) 0x419ca0 <start_pager>
gef➤  p &start_command
$2 = (int (*)(const char **)) 0x419d24 <start_pager+132>
gef➤  x/40i 0x419d24
```

# When this binary print call graph?
**below routine**
- default is command_live()
- current is `=> 0x404b7e <main+1342>:	call   0x416940 <command_live>`
- if you want to debug child process, type `set follow-fork-mode child` in gdb.
- `command_live` -> `command_record` -> `command_replay` is called in command_live()
```

