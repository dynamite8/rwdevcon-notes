## Synopsis

Debugging lab exercises

## Lab1

1. (lldb) image lookup -n viewDidLoad UIKit
2.


List the modules
```(lldb) image list -b```

Module name comes after the regular expression match:
```(lldb) image lookup -rn (\s\w+\:){1,} URL_Watcher```

```(lldb) image lookup -rn (\w+\:){14,}```
