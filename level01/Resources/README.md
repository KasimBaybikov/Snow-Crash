Находим файл в которм есть упоминание об flag01
```
grep -r "flag01" /etc/ 2>/dev/null
```
> /etc/group:flag:x:1001:flag00,flag01,flag02,flag03,flag04,flag05,flag06,flag07,flag08,flag09,flag10,flag11,flag12,flag13,flag14\
/etc/group:flag01:x:3001:\
/etc/passwd:flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash

```
cat /etc/passwd | grep flag01
```
>flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash

Каждая строка файла описывает одного пользователя и содержит семь полей, разделённых двоеточиями:

1) регистрационное имя или логин;
2) хеш пароля (см. ниже);
3) идентификатор пользователя;
4) идентификатор группы по умолчанию;
5) информационное поле GECOS (см. ниже);
6) начальный (он же домашний) каталог;
7) регистрационная оболочка, или shell.

42hDRfypTqqnw - это хэш пароля flag01

```
docker build --rm -t i_level01 .;
docker run --name level01 -it i_level01 sh;
```

Используем bruteforce John The Ripper
```
john file
john file --show

```
> ?:abcdefg\
\
1 password hash cracked, 0 left
