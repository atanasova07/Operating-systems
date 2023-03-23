# Exercises on commands

* Направете копие на файла /etc/passwd във вашата home директория под името my_passwd
```
cp /etc/passwd my_passwd
```

* Направете директория practice-test в home директорията ви. Вътре направете директория test1. Можете ли да направите тези две неща наведнъж? Разгледайте нужната man страница. След това създайте празен файл вътре, който да се казва test.txt, преместете го в practice-test чрез релативни пътища.
```
mkdir -p practice-test/test1
touch practice-test/test1/test.txt
mv practice-test/test1/test.txt practice-test
```

* Създайте директорията practice/01 във вашата home директория. Създайте 3 файла в нея - f1, f2, f3. Копирайте файловете f1, f2, f3 от директорията practice/01/ в директория dir1, намираща се във вашата home директория. Ако нямате такава, създайте я.
```
mkdir -p practice/01
touch practice/01/{f1,f2,f3}
mkdir dir1
cp practice/01/{f1,f2,f3} dir1
```
* Нека файлът f2 бъде преместен в директория dir2, намираща се във вашата home директория и бъде преименуван на numbers.
```
mkdir dir2
mv practice/01/f2 dir2/numbers
```

* Отпечатайте имената на всички директории в директорията /home.
```
ls -d /home/*
```

* Създайте файл permissions.txt в home директорията си. За него дайте единствено - read права на потребителя създал файла, write and exec на групата, read and exec на всички останали. Направете го и с битове, и чрез "буквички".
```
touch permissions.txt
chmod 435 permissions.txt
chmod u=r,g=wx,o=rx permissions.txt
```

* За да намерите какво сте правили днес: намерете всички файлове в home директорията ви, които са променени в последния 1 час.
```
find -type f -cmin -60
```

* Копирайте /etc/services в home директорията си. Прочетете го с командата cat. (Ако този файл го няма, прочетете с cat произволен текстов файл напр. /etc/passwd)
```
cp /etc/services services
cat services
```

* Създайте symlink на файла /etc/passwd в home директорията ви (да се казва например passwd_symlink).
```
ln -s /etc/passwd passwd_symlink
```

* Изведете всички обикновени ("regular") файлове, които /etc и нейните преки поддиректории съдържат
```
find /etc -mindepth 1 -type f
```

* Изведете само първите 5 реда от /etc/services
```
head -n5 /etc/services
```

* Изведете всички обикновени ("regular") файлове, които само преките поддиректории на /etc съдържат
```
find /etc -madepth 1 -type f
```

* Изведете всички преки поддиректории на /etc
```
find /etc -maxdepth 1 -type d
```

* Създайте файл, който да съдържа само последните 10 реда от изхода на горната задача
```
shell
find /etc -maxdepth 1 -type d | tail > lastLinesEtc.txt
```

* Изведете обикновените файлове по-големи от 42 байта в home директорията ви
```
find . -type f -size +42c
```

* Изведете всички обикновени файлове в директорията /tmp които са от вашата група, които имат write права за достъп за група или за останалите(o=w)
```
find /tmp -type f -perm /g+w,o+w -group 'students'
```

* Изведете всички файлове, които са по-нови от practice/01/f1 
```
find . -cnewer practice/01/f1
```

* Изтрийте файловете в home директорията си по-нови от practice/01/f3 (подайте на rm опция -i за да може да изберете само тези които искате да изтриете).
```
find . -type f -cnewer practice/01/f1 -exec rm -i {}\;
```

* Намерете файловете в /bin, които могат да се четат, пишат и изпълняват от всички.
```
find /bin -perm 0777
```

* Копирайте всички файлове от /etc, които могат да се четат от всички, в
директория myetc в home директорията ви. Направете такава, ако нямате.
```
shell
mkdir myetc
find /etc -type -f -perm -444 -exec cp {} myetc \;
```

* от предната задача: когато вече сте получили myetc с файлове, архивирайте
всички от тях, които започват с 'c' в архив, който се казва c_start.tar
изтрийте директорията myetc и цялото и съдържание изтрийте архива c_start.tar
```
find myetc/ -iname 'c*' -exec tar -c -f c_start.tar {} \;
rm -r myetc
rm c_start.tar
```

* Използвайки едно извикване на командата find, отпечатайте броя на редовете във всеки обикновен файл в /etc директорията.
```
find /etc -type f -exec wc -l {} \;
```

* Копирайте най-малкия файл от тези, намиращи се в /etc, в home директорията си.
```
shell
cp /etc/$(ls -Sr /rtc | head -1) .
```
