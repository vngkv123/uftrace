# support arch
**Only 3 arch are supported now**
- `in cmd-record.c`
```
1391 static void check_binary(struct opts *opts)
1392 {   
1393     int fd;
1394     int chk;
1395     size_t i; 
1396     char elf_ident[EI_NIDENT];
1397     uint16_t e_type;
1398     uint16_t e_machine;
1399     uint16_t supported_machines[] = {
1400         EM_X86_64, EM_ARM, EM_AARCH64,
1401     };
```
