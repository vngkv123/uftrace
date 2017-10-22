# command_live analysis
**preview**
- `command_live() -> command_record() -> command_replay()`
- `command_replay()` show call graph
```
command_replay!
# DURATION    TID     FUNCTION
   0.342 us [ 4096] | __monstartup();
   0.285 us [ 4096] | __cxa_atexit();
            [ 4096] | main() {
   0.544 us [ 4096] |   geteuid();
   3.198 us [ 4096] |   printf();
   0.158 us [ 4096] |   printf();
 436.079 ms [ 4096] |   __isoc99_scanf();
   1.366 us [ 4096] |   printf();
   0.076 us [ 4096] |   printf();
 359.278 ms [ 4096] |   __isoc99_scanf();
   1.705 us [ 4096] |   printf();
   0.089 us [ 4096] |   printf();
 360.153 ms [ 4096] |   __isoc99_scanf();
   1.561 us [ 4096] |   printf();
   0.085 us [ 4096] |   printf();
 239.969 ms [ 4096] |   __isoc99_scanf();
   1.439 us [ 4096] |   printf();
   0.101 us [ 4096] |   printf();
 207.841 ms [ 4096] |   __isoc99_scanf();
   1.797 us [ 4096] |   printf();
   1.603  s [ 4096] | } /* main */
before pager
```

