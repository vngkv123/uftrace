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
  32 /* my function -> find vuln function */
  33 
  34 #define sym_num 21
  35 char *vuln_sym_list[sym_num] = {"exec", "system", "gets", "popen", "getenv", "strcpy", "strncpy", "strcat", "strncat",
  36         "memcpy", "bcopy", "printf", "sprintf", "snprintf", "__isoc99_scanf",  "getchar", "getc", "read",
  37             "recv", "tmp", "temp"};
  38 
  39 static int find_vuln_sym(char *__symname){
  40     for(int idx = 0; idx < sym_num; idx++){
  41         if(!strncmp(vuln_sym_list[idx], __symname, strlen(__symname)))
  42             return 1;
  43     }
  44     return 0;
  45 }
  ```
