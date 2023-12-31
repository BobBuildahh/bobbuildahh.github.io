---
layout: post
title:  "RU | 'ELF ARM - Basic Crackme' from root-me.org"
date:   2023-09-26 15:00:02 +0300
categories: reverse
---
![First](/assets/arm_basic_elf_00.jpg)

Сейчас будем решать небольшой крякми под архитектуру ARM, поэтому советую всем ознакомиться с теми ссылками, которые даются в материалах к заданию, ознакомиться с синтаксисом и командами. Ну а я не лох, поэтому в этой статье буду реверсить как подобается настоящему мужчине, то есть с декомпилятором.
![Second](/assets/arm_basic_elf_01.jpg)

Заходим в ghidra / IDA, загружаем файлик и понимаем что отладочной информации у нас нету, как и имён функций:
![Third](/assets/arm_basic_elf_02.jpg)

Перебирать все функции конечно можно, но правильнее будет ориентироваться по известным нам функциям, к примеру printf(). Переходим к ней и смотрим из какой функции она вызывалась:
![Four](/assets/arm_basic_elf_03.jpg)

Переходим основную функцию, открываем декомпилятор и смотрим что да как:
![Five](/assets/arm_basic_elf_04.jpg)

Давайте по порядку,  
В самом начале программа проверяет сколько аргументов мы ей передаём. Если мы передали 2 аргумента, то идём дальше, в противном случае - программа закрывается. Как мы знаем, первый аргумент это всегда имя программы, поэтому программу нужно вызвать с единственным аргументом из командной строки - с паролем. (см. 1)  
Затем идёт проверка на количество символов в введенном пароле, должно быть 6. (см. 2)  
Далее, видим на первый взгляд странную строку

>local_14 = -sVar1 + 6;

Длина строки (sVar1) превращается в такое же, но уже отрицательное число и суммируется с 6, то есть local_14 = -6 + 6. Это нам еще пригодится. (см. 3)

Пока пропустим пункт №4, перейдем к пятому и посмотрим на уравнение:

>status = local_14 + (s[3] ^ 0x72) + (uint)s[6];

Для успешного решения нужно чтобы переменная status была равна нулю. Давайте теперь разберёмся как получить нужный нам результат.  
Ну для начала смотрим что здесь не может быть отрицательных чисел, поэтому каждый операнд должен быть равен нулю. (см.5)

>local_14 = 0 уже при правильной длине пароля.  
s[6] = 0 (нулевой символ в нашей строке).  
s[3] ^ 0x72, если число xor'иться само с собой - результат = 0, поэтому s[3] = 72.


Далее идёт завершающий, четвёртый пункт (см. 4).  
Четвёртая буква нам известна, 72 = 'r' и теперь мы можем легко решить это уравнение:  

>password[0] = 73 | s  
password[1] = 74 | t  
password[2] = 6F | o  
password[3] = 72 | r  
password[4] = 6D | m  
password[5] = 73 | s