```
scp -P 4242 level02@<ip>:/home/user/level02/level02.pcap ./
```

```
chmod 644 level02.pcap;
docker build --rm -t ilevel02 .;
docker run --name level02 -it ilevel02 sh;
```

```
tcpdump -r level02.pcap -X
```
> reading from file level02.pcap, link-type EN10MB (Ethernet), snapshot length 16777216\
05:23:12.267566 IP 59.233.235.218.39247 > 59.233.235.223.12121: Flags [S], seq 2635601089, win 14600, options [mss 1460,sackOK,TS val 18592800 ecr 0,nop,wscale 7], length 0\
	0x0000:  4510 003c a0e1 4000 4006 4a3e 3be9 ebda  E..<..@.@.J>;...\
	0x0010:  3be9 ebdf 994f 2f59 9d18 14c1 0000 0000  ;....O/Y........\
	0x0020:  a002 3908 8fad 0000 0204 05b4 0402 080a  ..9.............\
	0x0030:  011b b420 0000 0000 0103 0307            ............

Внимательно изучив эту куралесицу понимаем, что
Просмотрев на длину пакетов можно вытащить по тому колличеству байт сколько указано в length каждого пакета:
Вышло:

```
66 74 5f 77 61 6e 64 72 7f 7f 7f 4e 44 52 65 6c 7f 4c 30 4c 0d 00 0d 0a 01
```

Загоним нашу последовтельность в python3

```
"\x66\x74\x5f\x77\x61\x6e\x64\x72\x7f\x7f\x7f\x4e\x44\x52\x65\x6c\x7f\x4c\x30\x4c\x0d\x00\x0d\x0a\x01"
```
> 'ft_wandr\x7f\x7f\x7fNDRel\x7fL0L\r\x00\r\n\x01'

Т.к \x7f это сисвол backspace получаем:
```
ft_waNDReL0L
```
