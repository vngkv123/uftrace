# find vuln function
```
 724             if(strncmp(symname, "printf", 6) == 0)
 725                 pr_red("vuln function : %s\n", symname);
 726             else{
 727                 /* here is default call graph function */
 728                 pr_out("%s%s%s\n", symname, args, retval);
 729             }
 ```

# add vuln_sym_list
`32 char *vuln_sym_list[48];`

# add find_vuln_sym
```
  34 static int find_vuln_sym(char *__symname){
  35     for(int idx = 0; vuln_sym_list; vuln_sym_list[idx++]){
  36        if(!strncmp(vuln_sym_list[i], __symname, strlen(__symname)))
  37            return 1;
  38     }
  39     return 0;
  40 } 
  ```
