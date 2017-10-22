# find vuln function
```
 724             if(strncmp(symname, "printf", 6) == 0)
 725                 pr_red("vuln function : %s\n", symname);
 726             else{
 727                 /* here is default call graph function */
 728                 pr_out("%s%s%s\n", symname, args, retval);
 729             }
 ```
