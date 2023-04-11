# Exercises on scripts

* Да се напише shell скрипт, който приканва потребителя да въведе низ (име) и изпечатва "Hello, низ"
```shell
#!/bin/bash

read -p "What is your name: " name
echo "Hello, ${name}"
```

* Да се напише shell скрипт, който приема точно един параметър и проверява дали подаденият му параметър се състои само от букви и цифри.
```shell
#!/bin/bash

if echo "${1}" | grep -Eq '^[a-zA-Z0-9]*$'; then
    echo "true"
else
    echo "false"
fi
```

* Да се напише shell скрипт, който приканва потребителя да въведе низ - потребителско име на потребител от системата - след което извежда на стандартния изход колко 
активни сесии има потребителят в момента.
```shell
#!/bin/bash

read -p "Username: " username
echo "Active sessions: " $(who | grep "^${username}" | wc -l)
```

* Да се напише shell скрипт, който приканва потребителя да въведе пълното име на директория и извежда на стандартния изход подходящо съобщение за броя на всички файлове 
и всички директории в нея.
```shell
#!/bin/bash

read -p "Enter directory: " dir
if [ ! -d $dir ]
then
    echo "$dir is not a dir"
    exit
fi

num_dirs=$(find $dir -mindepth 1 2>/dev/null -type d | wc -l)
num_files=$(find $dir -type f 2>/dev/null | wc -l)
echo "Num of files: ${num_files}"
echo "Num of dirs: ${num_dirs}"
```

* Да се напише shell скрипт, който чете от стандартния вход имената на 3 файла, обединява редовете на първите два (man paste), подрежда ги по азбучен ред и резултата 
записва в третия файл.
```shell
#!/bin/bash

read -p "Input 3 file names: " f1 f2 f3
cat $f1 $f2 | sort -d -i >> $f3
```

* Да се напише shell скрипт, който чете от стандартния вход име на файл и символен низ, проверява дали низа се съдържа във файла и извежда на стандартния изход кода 
на завършване на командата с която сте проверили наличието на низа. 
NB! Символният низ може да съдържа интервал (' ') в себе си.
```shell
#!/bin/bash

read -p "Input file name and string: " file string

cat "$file" | grep -q "$string"
echo "The exit status is: $?"
```

* Имате компилируем (a.k.a няма синтактични грешки) source file на езика C. Напишете shell script, който да покaзва колко е дълбоко най-дълбокото nest-ване (влагане).
Примерен .c файл:

#include <stdio.h>
```c
int main(int argc, char *argv[]) {

  if (argc == 1) {
		printf("There is only 1 argument");
	} else {
		printf("There are more than 1 arguments");
	}

	return 0;
}
```

Тук влагането е 2, понеже имаме main блок, а вътре в него if блок.

Примерно извикване на скрипта:

./count_nesting sum_c_code.c

Изход:
The deepest nesting is 2 levels

```shell
#!/bin/bash

grep -o "[{}]" "${1}" | uniq -c | awk \
    'BEGIN {max=0; count=0} \
    $2 == "{" {count += $1} \
    $2 == "}" {count -= $1} \
    count > max {max=count} \
    END {print "The deepest nesting is "  max " levels"}'
```

* Напишете shell script, който да приема параметър име на директория, от която взимаме файлове, и опционално експлицитно име на директория, в която ще копираме файлове. Скриптът да копира файловете със съдържание, променено преди по-малко от 45 мин, от първата директория във втората директория. Ако втората директория не е подадена по име, нека да получи такова от днешната дата във формат, който ви е удобен. При желание новосъздадената директория да се архивира.
```shell
!/bin/bash

if [[ "${#}" -ne 2 ]]; then
    $2="$(date +'%Y-%m-%d')"
    mkdir $2
fi

find $1 -type f -mmin -45 -exec cp {} $2 \;

#find $1 -type f -mmin -45 | while read FILE; do
#   cp $FILE $2
#done
```

