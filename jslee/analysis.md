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


# gdb-peda's ftrace vs uftrace
**dynamically linked binary**
```
aSiagaming-peda$ ftrace
Tracing calls match '', Ctrl-C to stop...
   dep:02 => 0x40058b <main+37>:	call   0x400450 <asprintf@plt>
          |-- arg[0]: 0x7fffffffea58 --> 0x0
          |-- arg[1]: 0x40063c ("This is %s")
          |-- arg[2]: 0x400634 --> 0x4345534f464e49 ('INFOSEC')
   dep:02 => 0x400597 <main+49>:	call   0x400430 <puts@plt>
          |-- arg[0]: 0x602080
This is INFOSEC
End of execution
```

**statically linked binary**
```
Breakpoint 1, 0x00000000004009ae in main ()
aSiagaming-peda$ ftrace
Tracing calls match '', Ctrl-C to stop...
    dep:03 => 0x4009d3 <main+37>:	call   0x40f320 <asprintf>
           |-- arg[0]: 0x7fffffffea08 --> 0x4015e0 (<__libc_csu_fini>:	push   rbx)
           |-- arg[1]: 0x4a100c ("This is %s")
           |-- arg[2]: 0x4a1004 --> 0x4345534f464e49 ('INFOSEC')
     dep:04 => 0x40f3a2 <asprintf+130>:	call   0x411490 <vasprintf>
      dep:05 => 0x4114ab <vasprintf+27>:	call   0x41dc00 <malloc>
             |-- arg[0]: 0x64 ('d')
       dep:06 => 0x41dc4c <malloc+76>:	call   0x41b730 <_int_malloc>
              |-- arg[0]: 0x6ca800 --> 0x100000001
              |-- arg[1]: 0x64 ('d')
      dep:05 => 0x4114da <vasprintf+74>:	call   0x4156b0 <_IO_no_init>
             |-- arg[0]: 0x7fffffffe7f0 --> 0x0
             |-- arg[1]: 0x8000
             |-- arg[2]: 0xffffffff
             |-- arg[3]: 0x0
             |-- arg[4]: 0x0
      dep:05 => 0x4114f9 <vasprintf+105>:	call   0x416e30 <_IO_str_init_static_internal>
             |-- arg[0]: 0x7fffffffe7f0 --> 0x0
             |-- arg[1]: 0x6d0bf0 --> 0x0
             |-- arg[2]: 0x64 ('d')
             |-- arg[3]: 0x6d0bf0 --> 0x0
        dep:07 => 0x416e60 <_IO_str_init_static_internal+48>:	call   0x414f90 <_IO_setb>
               |-- arg[0]: 0x7fffffffe7f0 --> 0x0
               |-- arg[1]: 0x6d0bf0 --> 0x0
               |-- arg[2]: 0x6d0c54 --> 0x203b100000000
               |-- arg[3]: 0x0
      dep:05 => 0x411523 <vasprintf+147>:	call   0x454a80 <vfprintf>
             |-- arg[0]: 0x7fffffffe7f0 --> 0x0
             |-- arg[1]: 0x4a100c ("This is %s")
             |-- arg[2]: 0x7fffffffe918 --> 0x3000000010
       dep:06 => 0x454b13 <vfprintf+147>:	call   0x42cdd0 <strchrnul>
              |-- arg[0]: 0x4a100c ("This is %s")
              |-- arg[1]: 0x25 ('%')
       dep:06 => 0x454b4e <vfprintf+206>:	call   QWORD PTR [rax+0x38]
              |-- arg[0]: 0x7fffffffe7f0 --> 0x0
              |-- arg[1]: 0x4a100c ("This is %s")
              |-- arg[2]: 0x8
              |-- arg[3]: 0x8
       dep:06 => 0x455d5d <vfprintf+4829>:	call   QWORD PTR [rax+0x38]
              |-- arg[0]: 0x7fffffffe7f0 --> 0x0
              |-- arg[1]: 0x4a1004 --> 0x4345534f464e49 ('INFOSEC')
              |-- arg[2]: 0x7
       dep:06 => 0x455188 <vfprintf+1800>:	call   0x42cdd0 <strchrnul>
              |-- arg[0]: 0x4a1016 --> 0x2f7573632f2e2e00 ('')
              |-- arg[1]: 0x25 ('%')
       dep:06 => 0x4551cc <vfprintf+1868>:	call   QWORD PTR [rax+0x38]
              |-- arg[0]: 0x7fffffffe7f0 --> 0x0
              |-- arg[1]: 0x4a1016 --> 0x2f7573632f2e2e00 ('')
              |-- arg[2]: 0x0
              |-- arg[3]: 0x4a1016 --> 0x2f7573632f2e2e00 ('')
      dep:05 => 0x411583 <vasprintf+243>:	call   0x41dc00 <malloc>
             |-- arg[0]: 0x10
       dep:06 => 0x41dc4c <malloc+76>:	call   0x41b730 <_int_malloc>
              |-- arg[0]: 0x6ca800 --> 0x100000001
              |-- arg[1]: 0x10
      dep:05 => 0x41159f <vasprintf+271>:	call   0x42c210 <memcpy>
             |-- arg[0]: 0x6d0c60 --> 0x0
             |-- arg[1]: 0x6d0bf0 --> 0x0
             |-- arg[2]: 0xf
      dep:05 => 0x4115a7 <vasprintf+279>:	call   0x41dfa0 <free>
             |-- arg[0]: 0x6d0bf0 --> 0x0
        dep:07 => 0x41dfe2 <free+66>:	call   0x4199b0 <_int_free>
               |-- arg[0]: 0x6ca800 --> 0x100000001
               |-- arg[1]: 0x6d0be0 --> 0x0
               |-- arg[2]: 0x0
    dep:03 => 0x4009df <main+49>:	call   0x40fab0 <puts>
           |-- arg[0]: 0x6d0c60 --> 0x0
     dep:04 => 0x40fab7 <puts+7>:	call   0x423840 <strlen>
     dep:04 => 0x40fb55 <puts+165>:	call   QWORD PTR [rax+0x38]
            |-- arg[0]: 0x6ca300 --> 0xfbad2084
            |-- arg[1]: 0x6d0c60 --> 0x0
            |-- arg[2]: 0xf
      dep:05 => 0x412c9a <_IO_new_file_xsputn+170>:	call   QWORD PTR [rax+0x18]
             |-- arg[0]: 0x6ca300 --> 0xfbad2084
             |-- arg[1]: 0xffffffff
       dep:06 => 0x414053 <_IO_new_file_overflow+451>:	call   0x414ff0 <_IO_doallocbuf>
              |-- arg[0]: 0x6ca300 --> 0xfbad2084
              |-- arg[1]: 0xffffffff
              |-- arg[2]: 0x0
              |-- arg[3]: 0xfbad2084
        dep:07 => 0x415021 <_IO_doallocbuf+49>:	call   QWORD PTR [rax+0x68]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
         dep:08 => 0x462c7f <_IO_file_doallocate+31>:	call   QWORD PTR [rax+0x90]
                |-- arg[0]: 0x6ca300 --> 0xfbad2084
                |-- arg[1]: 0x7fffffffe8a0 --> 0xa ('\n')
         dep:08 => 0x462cb0 <_IO_file_doallocate+80>:	call   0x41dc00 <malloc>
                |-- arg[0]: 0x400
          dep:09 => 0x41dc4c <malloc+76>:	call   0x41b730 <_int_malloc>
                 |-- arg[0]: 0x6ca800 --> 0x100000001
                 |-- arg[1]: 0x400
        dep:07 => 0x41b886 <_int_malloc+342>:	call   0x418020 <malloc_consolidate>
               |-- arg[0]: 0x6ca800 --> 0x100000001
         dep:08 => 0x462cce <_IO_file_doallocate+110>:	call   0x414f90 <_IO_setb>
                |-- arg[0]: 0x6ca300 --> 0xfbad2084
                |-- arg[1]: 0x6d0c80 --> 0x0
                |-- arg[2]: 0x6d1080 --> 0x0
                |-- arg[3]: 0x1
      dep:05 => 0x412cd2 <_IO_new_file_xsputn+226>:	call   0x4150c0 <_IO_default_xsputn>
             |-- arg[0]: 0x6ca300 --> 0xfbad2084
             |-- arg[1]: 0x6d0c60 --> 0x0
             |-- arg[2]: 0xf
             |-- arg[3]: 0x400
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x54 ('T')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x68 ('h')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x69 ('i')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x73 ('s')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x20 (' ')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x69 ('i')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x73 ('s')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x20 (' ')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x49 ('I')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x4e ('N')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x46 ('F')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x4f ('O')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x53 ('S')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x45 ('E')
        dep:07 => 0x41513d <_IO_default_xsputn+125>:	call   QWORD PTR [rax+0x18]
               |-- arg[0]: 0x6ca300 --> 0xfbad2084
               |-- arg[1]: 0x43 ('C')
     dep:04 => 0x40fc15 <puts+357>:	call   0x414c80 <__overflow>
            |-- arg[0]: 0x6ca300 --> 0xfbad2084
            |-- arg[1]: 0xa ('\n')
      dep:05 => 0x413f76 <_IO_new_file_overflow+230>:	call   0x4133c0 <_IO_new_do_write>
             |-- arg[0]: 0x6ca300 --> 0xfbad2084
             |-- arg[1]: 0x6d0c80 --> 0x0
             |-- arg[2]: 0x10
             |-- arg[3]: 0xfbad2a84
       dep:06 => 0x413436 <_IO_new_do_write+118>:	call   QWORD PTR [rax+0x78]
              |-- arg[0]: 0x6ca300 --> 0xfbad2084
              |-- arg[1]: 0x6d0c80 --> 0x0
              |-- arg[2]: 0x10
        dep:07 => 0x41260a <_IO_new_file_write+138>:	call   0x43f3d0 <write>
               |-- arg[0]: 0x1
               |-- arg[1]: 0x6d0c80 --> 0x0
               |-- arg[2]: 0x10
This is INFOSEC
   dep:02 => 0x400c38 <generic_start_main+584>:	call   0x40ea10 <exit>
          |-- arg[0]: 0x0
    dep:03 => 0x40ea1e <exit+14>:	call   0x40e8e0 <__run_exit_handlers>
           |-- arg[0]: 0x0
           |-- arg[1]: 0x6ca0a0 --> 0x6cc160 --> 0x0
           |-- arg[2]: 0x1
     dep:04 => 0x40e9d6 <__run_exit_handlers+246>:	call   rdx
            |-- arg[0]: 0x0
            |-- arg[1]: 0x0
            |-- arg[2]: 0x4015e0 (<__libc_csu_fini>:	push   rbx)
     dep:04 => 0x401600 <__libc_csu_fini+32>:	call   QWORD PTR [rbx*8+0x6c9ee0]
     dep:04 => 0x401600 <__libc_csu_fini+32>:	call   QWORD PTR [rbx*8+0x6c9ee0]
      dep:05 => 0x40094d <__do_global_dtors_aux+13>:	call   0x4008c0 <deregister_tm_clones>
             |-- arg[0]: 0x0
      dep:05 => 0x400961 <__do_global_dtors_aux+33>:	call   rax
             |-- arg[0]: 0x4be300 --> 0x14
     dep:04 => 0x40e975 <__run_exit_handlers+149>:	call   QWORD PTR [rbp+0x0]
      dep:05 => 0x4144fc <_IO_cleanup+284>:	call   QWORD PTR [rax+0x58]
             |-- arg[0]: 0x6ca300 --> 0xfbad2084
             |-- arg[1]: 0x0
             |-- arg[2]: 0x0
      dep:05 => 0x411ba4 <_IO_new_file_setbuf+4>:	call   0x4153e0 <_IO_default_setbuf>
       dep:06 => 0x4153f4 <_IO_default_setbuf+20>:	call   QWORD PTR [rax+0x60]
     dep:04 => 0x40e987 <__run_exit_handlers+167>:	call   0x43e9c0 <_exit>
            |-- arg[0]: 0x0
```
