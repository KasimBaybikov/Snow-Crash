```
ls -l
```
> total 0

На уровне ничего нет и ничего не запущено

остается только реверсить getflag


```
which getflag
```
> /bin/getflag


```
gdb
```

```
file /bin/getflag
```
> Reading symbols from /bin/getflag...(no debugging symbols found)...done.\
(gdb)

```
b main
```
> Breakpoint 1 at 0x804894a

s - Продолжайте выполнять вашу программу, пока управление не перейдет к другой строке исходного кода, затем остановите ее и верните управление GDB. 
n - Перейдите к следующей исходной строке в текущем (самом внутреннем) кадре стека. Это похоже на step, но вызовы функций, которые появляются в строке кода, выполняются без остановки.
c - Возобновить выполнение программы с того адреса, где ваша программа остановилась в последний раз; любые точки останова, установленные по этому адресу, игнорируются.
b - установить breakpoint
si - Выполните одну машинную инструкцию, затем остановитесь и вернитесь в отладчик.
ni - Выполнение одной машинной инструкции, но если это вызов функции, продолжайте до тех пор, пока функция не вернется.
until - Продолжайте работу до тех пор, пока не будет достигнута исходная строка после текущей строки в текущем кадре стека.
info r - посмотерть регистры
disas - увидеть дисасемблер

Нам нужно подменить результат выполнения ptrace, потому что он падает с ошибкой, и подать нужное значение после getuid

```
b *0x08048989
```
> Breakpoint 2 at 0x8048989

```
run
```
> Starting program: /bin/getflag\
\
Breakpoint 1, 0x0804894a in main ()

```
n
```
> Single stepping until exit from function main,\
which has no line number information.\
\
Breakpoint 2, 0x08048989 in main ()

```
ni
```
> 0x0804898e in main ()

```
print $eax
```
> $1 = -1

-1 Сигнализирует о том, что произошла ошибка, заменим значение

```
set $eax=0
```

Поставим еще 1 breakpoint
```
b getuid
```

```
n
```

Доходим до 0x08048b02 <+444>:	mov    %eax,0x18(%esp) и ставим eax в нужное нам значение:

```
set $eax=0xbc6
```

```
n
```
> Single stepping until exit from function main,
which has no line number information.
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
0xb7e454d3 in \_\_libc_start_main () from /lib/i386-linux-gnu/libc.so.6
