```
ls -l
```
> total 12\
-rwsr-sr-x 1 flag09 level09 7640 Mar  5  2016 level09\
----r--r-- 1 flag09 level09   26 Mar  5  2016 token

Пробуем запустить 
```
./level09
```
> You need to provied only one arg.

Смотрим внутренности:
```
ltrace ./level09
```
> \_\_libc_start_main(0x80487ce, 1, 0xbffff7b4, 0x8048aa0, 0x8048b10 <unfinished ...>\
ptrace(0, 0, 1, 0, 0xb7e2fe38)                                                                                = -1\
puts("You should not reverse this"You should not reverse this\
)                                                                           = 28\
+++ exited (status 1) +++

"You should not reverse this", значит поищем другой подход

Попробуем запускать с параметрами:

```
./level09 1
```
> 1

```
./level09 1111111
```
> 1234567

Видно, что скрипт увеличивает значение символа на позицию в строке

Посмотрим теперь содержимое токена:

```
cat token
```
f4kmm6p|=�p�n��DB�Du{��

Посмотрим непечатаемые символы:

```
hexdump -C token
```
> 00000000  66 34 6b 6d 6d 36 70 7c  3d 82 7f 70 82 6e 83 82  |f4kmm6p|=..p.n..|\
00000010  44 42 83 44 75 7b 7f 8c  89 0a                    |DB.Du{....|\
0000001a

Загоним все это в Питончика:

```
docker build --rm -t ilevel09 .;
docker run --name level09 -it ilevel09 sh;
```

И напишем простейший декодер
```
python3
a = [0x66, 0x34, 0x6b, 0x6d, 0x6d, 0x36, 0x70, 0x7c, 0x3d, 0x82, 0x7f, 0x70, 0x82, 0x6e, 0x83, 0x82, 0x44, 0x42, 0x83, 0x44, 0x75, 0x7b, 0x7f, 0x8c,  0x89, 0x0a]
a
for i in range(len(a) - 1):
    print(chr(a[i]-i), end='')
```
> f3iji1ju5yuevaus41q1afiuq>>>
