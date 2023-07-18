# Exercises on pipes

* Сортирайте /etc/passwd лексикографски по поле UserID.
```shell
cat /etc/passwd | sort -t ':' -k3
```

* Сортирайте /etc/passwd числово по поле UserID.
```shell
cat /etc/passwd | sort -t ':' -n -k3
```

* Изведете само 1-ва и 5-та колона на файла /etc/passwd спрямо разделител ":".
```shell
cat /etc/passwd | cut -d ':' -f 1,5
```

* Изведете съдържанието на файла /etc/passwd от 2-ри до 6-ти символ.
```shell
cat /etc/passwd | cut -c 2-6
```

* Отпечатайте потребителските имена и техните home директории от /etc/passwd.
```shell
cat /etc/passwd | cut -d ':' -f 5,6
```

* Отпечатайте втората колона на /etc/passwd, разделена спрямо символ '/'.
```shell
cat /etc/passwd | cut -d '/' -f 2
```

* Изведете броя на байтовете в /etc/passwd. Изведете броя на символите в /etc/passwd. Изведете броя на редовете в /etc/passwd.
```shell
cat /etc/passwd | wc -c
cat /etc/passwd | wc -m
cat /etc/passwd | wc -l
```

* С отделни команди, извадете от файл /etc/passwd:
- първите 12 реда
- първите 26 символа
- всички редове, освен последните 4
- последните 17 реда
- 151-я ред (или друг произволен, ако нямате достатъчно редове)
- последните 4 символа от 13-ти ред (символът за нов ред не е част от реда)
```shell
head /etc/passwd -n12
head /etc/passwd -c26
head /etc/passwd -n-4
tail /etc/passwd -n17
cat /etc/passwd | head -n151 | tail -n1
cat /etc/passwd | head -n13 | tail -c5
```

* Запаметете във файл в своята home директория резултатът от командата `df -P`. Напишете команда, която извежда на екрана съдържанието на този файл, без първия ред 
(хедъра), сортирано по второ поле (numeric).
```shell 
df -P > dfFile
cat dfFile | sort -t ' ' -k2 -n | tail -n+2
```

* Запазете само потребителските имена от /etc/passwd във файл users във вашата home директория.
```shell
cat /etc/passwd | cut -d ':' -f1 > users
```

* Изпишете всички usernames от /etc/passwd с главни букви.
```shell
cat /etc/passwd | cut -d ':' -f1 | tr a-z A-Z
```

* Изведете реда от /etc/passwd, на който има информация за вашия потребител. Изведедете този ред и двата реда преди него. Изведете този ред, двата преди него, и трите 
след него. Изведете *само* реда, който се намира 2 реда преди реда, съдържащ информация за вашия потребител.
```shell
cat /etc/passwd | egrep -B2 "s0600018"
cat /etc/passwd | egrep -B2 -A3 "s0600018"
cat /etc/passwd | egrep -B2 "s0600018" | head -n1
```

* Изведете колко потребители не изпозват /bin/bash за login shell според /etc/passwd
```shell
cat /etc/passwd | grep -v bash | wc -l
```

* Изведете само имената на хората с второ име по-дълго от 6 (>6) символа според /etc/passwd
```shell
cat /etc/passwd | cut -d ':' -f5 | cut -d ',' -f1 | awk '{if(length($2) > 6) print $0}'
```

* Изведете имената на хората с второ име по-късо от 8 (<=7) символа според /etc/passwd 
```shell
cat /etc/passwd | cut -d ':' -f5 | cut -d ',' -f1 | awk '{if(length($2) < 8) print $0}'
```
* Изведете целите редове от /etc/passwd за хората от предишната задача
```shell

```

* Копирайте <РЕПО>/exercises/data/emp.data във вашата home директория.
Посредством awk, използвайки копирания файл за входнни данни, изведете:
- общия брой редове
- третия ред
- последното поле от всеки ред
- последното поле на последния ред
- всеки ред, който има повече от 4 полета
- всеки ред, чието последно поле е по-голямо от 4
- общия брой полета във всички редове
- броя редове, в които се среща низът Beth
- най-голямото трето поле и редът, който го съдържа
- всеки ред, който има поне едно поле
- всеки ред, който има повече от 17 знака
- броя на полетата във всеки ред и самият ред
- първите две полета от всеки ред, с разменени места
- всеки ред така, че първите две полета да са с разменени места
- всеки ред така, че на мястото на първото поле да има номер на реда
- всеки ред без второто поле
- за всеки ред, сумата от второ и трето поле
- сумата на второ и трето поле от всеки ред
```shell
cat emp.data | awk 'BEGIN {count = 0} {count += 1} END {print count}'
cat emp.data | awk 'NR == 3 {print $0}'
cat emp.data | awk '{print $NF}'
cat emp.data | awk 'END {print $NF}'
cat emp.data | awk 'NF > 4 {print $0}'
cat emp.data | awk '$NF > 4 {print $0}'
cat emp.data | awk '{count += NF} END {print count}'
cat emp.data | awk '/Beth/ {lined += 1} END {print lines}'
cat emp.data | awk '$3 > max {max = $3; line = $0} END {print max " " line}'
cat emp.data | awk '$3 > max {max = $3; line = $0} END {print max " " line}'
cat emp.data | awk 'NF >= 1 {print $0}'
cat emp.data | awk '/^.{17,}/ {print $0}'
cat emp.data | awk '{print NF " " $0}'
cat emp.data | awk '{print $2 " " $1}'
cat emp.data | awk '{temp = $1; $1 = $2; $2 = temp; print $0}'
cat emp.data | awk '{$1 = NR; print $0}'
cat emp.data | awk '{$1 = ""; print $0}'
cat emp.data | awk '{sum = $2 + $3; print sum}'
cat emp.data | awk '{sum += $2 +$3} END {print sum}'
```

* Намерете само Group ID-то си от файлa /etc/passwd.
```shell
cat /etc/passwd | grep  "s0600018" | cut -d ":" -f4
```

* Колко коментара има във файла /etc/services? Коментарите се маркират със символа #, след който всеки символ на реда се счита за коментар.
```shell
cat /etc/services | awk '/#/ {count += 1} END {print count}'
cat /etc/services | grep '#' | wc -l  # second option
```

* Колко файлове в /bin са 'shell script'-oве? (Колко файлове в дадена директория са ASCII text?)
```shell
find /bin/ -type f | xargs -I {} file {} | grep 'shell script' | wc -l
find /bin/ -type f | xargs -I {} file {} | grep 'ASCII' | wc -l
```

* Направете списък с директориите на вашата файлова система, до които нямате достъп. Понеже файловата система може да е много голяма, търсете до 3 нива на дълбочина.
```shell
find / -maxdepth 3 -type d -readable
```

* Създайте следната файлова йерархия в home директорията ви:
dir5/file1, dir5/file2, dir5/file3

Посредством vi въведете следното съдържание:
file1:
```
1
2
3
```

file2:
```
s
a
d
f
```

file3:
```
3
2
1
45
42
14
1
52
```

Изведете на екрана:
	  - статистика за броя редове, думи и символи за всеки един файл
	  - статистика за броя редове и символи за всички файлове
	  - общия брой редове на трите файла
    
```shell
wc -l -w -c dir5/file{1,2,3}
cat dir5/file{1,2,3} | wc -l -c
cat dir5/dile{1,2,3} | wc - l
```

* Във file2 (inplace) подменете всички малки букви с главни.
```shell
cat dir5/file2 | tr a-z A-Z > temp && mv temp dir5/file2
```

* Във file3 (inplace) изтрийте всички "1"-ци.
```shell
cat dir5/file3 | tr -d 1 > temp && mv temp dir5/file3
```

* Изведете статистика за най-често срещаните символи в трите файла.
```shell
cat dir5/file{1,2,3} | sort | uniq -c | sort -n -r -k1
```

* Направете нов файл с име по ваш избор, чието съдържание е конкатенирани
съдържанията на file{1,2,3}.
```shell
touch dir5/file4
cat dir5/file{1,2,3} >> dir5/file4
```

* Прочетете текстов файл file2 и направете всички главни букви малки като
запишете резултата във file1.
```shell
cat dir5/file2 | tr A-Z a-z > dir5/file1
```

* Намерете броя на символите, различни от буквата 'а' във файла /etc/passwd
```shell
cat /etc/passwd | sed 's/a//g' | wc -m
```

* Отпечатайте последната цифра на UID на всички редове между 28-ми и 46-ред в /etc/passwd.
```shell
cat /etc/passwd | cut -d ':' -f3 | head -n46 | tail -n+28 | awk '{print $1 % 10}'
or
cat /etc/passwd | cut -d ':' -f3 | head -n46 | tail -n+28 | rev | cut -c 1
or
 cat /etc/passwd | cut -d ':' -f3 | head -n46 | tail -n+28 | egrep -o '.$'
```

* Отпечатайте правата (permissions) и имената на всички файлове, до които имате
read достъп, намиращи се в директорията /tmp.
```shell
find /tmp -readable -printf "%M %f\n" 2>/dev/null
```
* Намерете имената на 10-те файла във вашата home директория, чието съдържание е
редактирано най-скоро. На първо място трябва да бъде най-скоро редактираният
файл. Намерете 10-те най-скоро достъпени файлове. (hint: Unix time)
```shell
find . -type f -printf "%T@ %f \n" | sort -k1 -n -r |  cut -d ' ' -f2 | head -n10
find . -type f -printf "%A@ %f \n" | sort -k1 -n -r |  cut -d ' ' -f2 | head -n10
```

* да приемем, че файловете, които съдържат C код, завършват на `.c` или `.h`.
Колко на брой са те в директорията `/usr/include`?
Колко реда C код има в тези файлове?
```shell
find /usr/include | grep '.[ch]$' | wc -l
cat $(find /usr/include | grep '.[ch]') 2>/dev/null | wc -l
```

* Даден ви е ASCII текстов файл - /etc/services. Отпечатайте хистограма на 10-те най-често срещани думи.
Дума наричаме непразна последователност от букви. Не правим разлика между главни и малки букви.
Хистограма наричаме поредица от редове, всеки от които има вида:
<брой срещания> <какво се среща толкова пъти>
```shell
cat /etc/services | grep -o '[a-zA-Z]*' | tr [A-Z] [a-z] | sort | uniq -c | sort -k1 -nr | head -n10
```

* Вземете факултетните номера на студентите (описани във файла
<РЕПО>/exercises/data/mypasswd.txt) от СИ и ги запишете във файл si.txt сортирани.
Студент е част от СИ, ако home директорията на този потребител (според
<РЕПО>/exercises/data/mypasswd.txt) се намира в /home/SI директорията.
```shell
 cat /srv/fmi-os/exercises/data/mypasswd.txt | grep '/home/SI/' | cut -d ':' -f1 | sed 's/s//g' | grep -v '[a-zA-Z]' | sort -n > si.txt
```

* За всяка група от /etc/group изпишете "Hello, <група>", като ако това е вашата група, напишете "Hello, <група> - I am here!".
```shell
awk -F ':' '$1=="students" {print "Hello, " $1 " -I am here"} $1!="students" {print "Hello, " $1}' /etc/group
```

* Shell Script-овете са файлове, които по конвенция имат разширение .sh. Всеки
такъв файл започва с "#!<interpreter>" , където <interpreter> указва на
операционната система какъв интерпретатор да пусне (пр: "#!/bin/bash",
"#!/usr/bin/python3 -u").
Намерете всички .sh файлове в директорията `/usr` и нейните поддиректории, и
проверете кой е най-често използваният интерпретатор.
```shell
 find /usr -type f -iname "*.sh" -exec head -n1 {} \; | grep "^#!" | cut -d '!' -f2 | sort -d | uniq -c | sort -k1 -nr | head -n1 | awk '{print $2}'
/bin/sh
```

* Изведете GID-овете на 5-те най-големи групи спрямо броя потребители, за които
съответната група е основна (primary).
(*) Изведете имената на съответните групи.
```shell
cat /etc/passwd | cut -d ':' -f4 | sort | uniq -c | sort -k1 -nr | head -n5 | awk '{print $2}' | xargs -I {} grep ':{}:' /etc/group | awk -F ':' '{print $1 " " $3}'
```

