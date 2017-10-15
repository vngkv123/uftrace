# debug with gef
**quit strange in asm code**
- start_pager, start_command is consolidated...
```
gef➤  p &start_pager
$1 = (void (*)(void)) 0x419ca0 <start_pager>
gef➤  p &start_command
$2 = (int (*)(const char **)) 0x419d24 <start_pager+132>
gef➤  x/40i 0x419d24
```
