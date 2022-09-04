```
ls -l
```
> total 8\
-rwsr-sr-x 1 flag13 level13 7303 Aug 30  2015 level13

Старый добрый SUID

```
cat level13
```
Смотрим, а там мясо....


```
./level13
```
> UID 2013 started us but we we expect 4242

Пробуем запускать, а он там ругается, что якобы у нас UID не тот, ну ладненько, лезем внутрь

```
strace ./level13
```
> execve("./level13", ["./level13"], [/* 20 vars */]) = 0\
...\
getuid32()                              = 2013\
getuid32()                              = 2013\
fstat64(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 0), ...}) = 0\
mmap2(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0xb7fda000\
write(1, "UID 2013 started us but we we ex"..., 42UID 2013 started us but we we expect 4242\
) = 42\
...

После запуска strace понимаем, что вызывается getuid(), подменить его извне не получится
Выходит надо смотреть что внутри, смотрим в gdb

```
gdb level13
```
> GNU gdb (Ubuntu/Linaro 7.4-2012.04-0ubuntu2.1) 7.4-2012.04\
Copyright (C) 2012 Free Software Foundation, Inc.\
License GPLv3+: GNU GPL version 3 or later \<http://gnu.org/licenses/gpl.html>\
This is free software: you are free to change and redistribute it.\
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"\
and "show warranty" for details.\
This GDB was configured as "i686-linux-gnu".\
For bug reporting instructions, please see:\
\<http://bugs.launchpad.net/gdb-linaro/>...\
Reading symbols from /home/user/level13/level13...(no debugging symbols found)...done.

```
disas main
```
> Dump of assembler code for function main:\
   0x0804858c <+0>:		push   %ebp\
   0x0804858d <+1>:		mov    %esp,%ebp\
   0x0804858f <+3>:		and    $0xfffffff0,%esp\
   0x08048592 <+6>:		sub    $0x10,%esp\
   0x08048595 <+9>:		call   0x8048380 \<getuid@plt>\
   0x0804859a <+14>:	cmp    $0x1092,%eax\
   0x0804859f <+19>:	je     0x80485cb \<main+63>\
   0x080485a1 <+21>:	call   0x8048380 \<getuid@plt>\
   0x080485a6 <+26>:	mov    $0x80486c8,%edx\
   0x080485ab <+31>:	movl   $0x1092,0x8(%esp)\
   0x080485b3 <+39>:	mov    %eax,0x4(%esp)\
   0x080485b7 <+43>:	mov    %edx,(%esp)\
   0x080485ba <+46>:	call   0x8048360 \<printf@plt>\
   0x080485bf <+51>:	movl   $0x1,(%esp)\
   0x080485c6 <+58>:	call   0x80483a0 \<exit@plt>\
   0x080485cb <+63>:	movl   $0x80486ef,(%esp)\
   0x080485d2 <+70>:	call   0x8048474 \<ft_des>\
   0x080485d7 <+75>:	mov    $0x8048709,%edx\
   0x080485dc <+80>:	mov    %eax,0x4(%esp)\
   0x080485e0 <+84>:	mov    %edx,(%esp)\
   0x080485e3 <+87>:	call   0x8048360 \<printf@plt>\
   0x080485e8 <+92>:	leave\
   0x080485e9 <+93>:	ret\
End of assembler dump.

В одной из строк сравнивается регистр с числом, нам необходимо подставить в этот момент времени в регистр нужное число, чтобы код пошел дальше
   0x0804859a <+14>:	cmp    $0x1092,%eax\

Поставим breakpoint в нужном месте:
```
b *0x0804859a
```
> Breakpoint 1 at 0x804859a

Запустим gdb
```
r
```
> Starting program: /home/user/level13/level13\
\
Breakpoint 1, 0x0804859a in main ()

Установим нужное значение
```
set $eax=0x1092
```

```
c
```
> Continuing.\
your token is 2A31L79asukciNyi8uppkEuSx\
[Inferior 1 (process 3311) exited with code 050]\
