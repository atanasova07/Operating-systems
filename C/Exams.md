* Напишете програма на C, която приема параметър - име на (двоичен) файл с байтове.
Програмата трябва да сортира файла
```c
#include <fcntl.h>
#include <stdlib.h>
#include <stdio.h>
#include <stdint.h>
#include <unistd.h>
#include <err.h>

int main(int argc, char** argv) {
        if (argc != 2) {
                errx(1, "ERROR: Invalid number of arguments!");
        }

        int fd;
        if ((fd = open(argv[1], O_RDWR)) == -1) {
                err(2, "ERROR: Could not open file %s!", argv[1]);
        }

        uint8_t bytes[256];
        for (int i = 0; i < 256; i++) {
                bytes[i] = 0;
        }

        uint8_t symb;
        int bytesCount = 0;
        while ((bytesCount = read(fd, &symb, sizeof(symb))) > 0) {
                bytes[symb] += 1;
        }

        if (bytesCount < 0) {
                err(3, "ERROR: Could not read from %s file", argv[1]);
        }

        if (lseek(fd, 0, SEEK_SET) < 0) {
                err(4, "ERROR: Could not lseek in %s file", argv[1]);
        }

        for (int i = 0; i < 256; i++) {
                uint8_t ch = i;
                for(int j = 0; j < bytes[i]; j++) {
                        if(write(fd, &ch, sizeof(ch)) == -1) {
                                err(5, "ERROR: Could not write to %s file", argv[1]);
                        }
                }
        }

        close(fd);

}
```

* Двоичните файлове f1 и f2 съдържат 32 битови числа без знак (uint32_t). Файлът f1
е съдържа n двойки числа, нека i-тата двойка е < xi, yi >. Напишете програма на C, която извлича
интервалите с начало xi и дължина yi от файла f2 и ги записва залепени в изходен файл f3.   
Пример:  
• f1 съдържа 4 числа (2 двойки): 30000, 20, 19000, 10   
• програмата записва в f3 две поредици 32-битови числа, взети от f2 както следва:   
• най-напред се записват числата, които са на позиции 30000, 30001, 30002, ... 30019.   
• след тях се записват числата от позиции 19000, 19001, ... 19009.   

```c
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <fcntl.h>
#include <err.h>

struct pair{
        uint32_t start;
        uint32_t length;
};

int main(int argc, char** argv) {
        if (argc != 4) {
                errx(1, "ERROR: Invalid number of arguments!");
        }

        int fd1, fd2, fd3;

        if((fd1 = open(argv[1], O_RDONLY)) == -1) {
                err(2, "ERROR: Could not open file %s for reading", argv[1]);
        }

        if((fd2 = open(argv[2], O_RDONLY)) == -1) {
                err(2, "ERROR: Could not open file %s for reading", argv[2]);
        }

        if((fd3 = open(argv[3], O_WRONLY | O_CREAT, O_IRWXU)) == -1) {
                err(3, "ERROR: Could not open file %s for writing", argv[3]);
        }

        struct stat s;
        if(fstat(fd1, &s) == -1) {
                err(4, "ERROR: Could not fstat");
        }

        if(s.st_size % 8 != 0) {
                errx(5, "ERROR: %s file size must be divisible by 8", argv[1]);
        }

        struct pair p;
        int bytesCount;

        while((bytesCount = read(fd1, &p, sizeof(p))) > 0) {
                if(lseek(fd2, p.start, SEEK_SET) == -1){
                        err(6, "ERROR: Could not lseek");
                }
                uint32_t buf;

                for(uint32_t i = 0; i < p.length; i++) {
                        if(read(fd2, &buf, sizeof(buf)) == -1){
                                err(7, "ERROR: Could not read from %s file", argv[2]);
                        }
                        if(write(fd3, &buf, sizeof(buf)) != sizeof(buf)){
                                err(8, "ERROR: Could not write to %s file", argv[3]);

                        }
                }
        }

        if(bytesCount == -1) {
                err(9, "ERROR: Could not read from %s file", argv[1]);
        }
        close(fd1);
        close(fd2);
        close(fd3);

}
```

* Напишете програма на C, която да работи подобно на командата cat, реализирайки
само следната функционалност:     
• програмата извежда на STDOUT   
• ако няма подадени параметри, програмата чете от STDIN   
• ако има подадени параметри – файлове, програмата последователно ги извежда  
• ако някой от параметрите започва с тире (-), програмата да го третира като специално име за STDIN   
Примерно извикване:  
$ ./main f - g    
– извежда съдържанието на файла f, после STDIN, след това съдържанието на файла g

```c
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <err.h>
#include <string.h>

void writeToStdout(int fd);
void writeToStdout(int fd){
        int bytes_count;
        char buf;
        while((bytes_count=read(fd, &buf, sizeof(buf))) > 0){
                if(write(1, &buf, sizeof(buf)) == -1){
                        err(2, "ERROR: Could not write to stdout");
                }
        }
        if(bytes_count == -1){
                err(1, "ERROR: Could not read!");
        }
}

int main(int argc, char** argv){
        if(argc == 1){
                writeToStdout(0);
        }
        int fd;
        for(int i = 1; i < argc; i++){
                if(strcmp(argv[i], "-") == 0){
                        writeToStdout(0);
                } else {
                        if((fd = open(argv[i], O_RDONLY)) == -1){
                                err(3, "ERROR: Could not open %s file for reading!", argv[i]);
                        }
                        writeToStdout(fd);
                        if(close(fd) == -1){
                                err(4, "ERROR: Could not close file");
                        }
                }
        }
        exit(0);
}
```

* Напишете програма на C, която приема три параметъра – имена на двоични файлове.
Примерно извикване:  
$ ./main patch.bin f1.bin f2.bin   
Файловете patch.bin и f1.bin съществуват, и на тяхна база програмата трябва да създаде f2.bin.   
Файлът patch.bin се състои от две секции – 16 байтов хедър и данни. На базата на хедъра програмата трябва да може да интерпретира съдържанието на файла. Структурата на хедъра е:   
• uint32_t, magic – магическа стойност 0xEFBEADDE, която дефинира, че файлът следва тази спецификация  
• uint8_t, header version – версия на хедъра, с единствена допустима стойност за момента 0x01, която дефинира останалите байтове от хедъра както следва:  
– uint8_t, data version – версия (описание) на използваните структури в секцията за данни на файла   
– uint16_t, count – брой записи в секцията за данни   
– uint32_t, reserved 1 – не се използва   
– uint32_t, reserved 2 – не се използва   
Възможни структури в секцията за данни на файла спрямо data version:   
• при версия 0x00    
– uint16_t, offset    
– uint8_t, original byte   
– uint8_t, new byte   
• при версия 0x01   
– uint32_t, offset    
– uint16_t, original word   
– uint16_t, new word   
• забележка: и при двете описани версии offset е отместване в брой елементи спрямо началото на файла  
Двоичните файлове f1.bin и f2.bin се третират като състоящи се от елементи спрямо data version в patch.bin.   
Програмата да създава файла f2.bin като копие на файла f1.bin, но с отразени промени на базата на файла patch.bin, при следния алгоритъм:
• за всяка наредена тройка от секцията за данни на patch.bin, ако на съответният offset в оригиналния файл f1.bin е записан елементът original byte/word, в изходният файл се записва new byte/word. Ако не е записан такъв елемент или той не съществува, програмата да прекратява изпълнението си по подходящ начин;    
• всички останали елементи се копират директно.    
Наредените тройки в секцията за данни на файла patch.bin да се обработват последователно.   
Обърнете внимание на обработката за грешки и съобщенията към потребителя – искаме програмата да бъде удобен и валиден инструмент.

```c
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <err.h>
#include <stdint.h>

struct header{
        uint32_t magic;
        uint8_t header_version;
        uint8_t data_version;
        uint16_t count;
        uint32_t _;
        uint32_t __;
};

struct data00{
        uint16_t offset;
        uint8_t orig_byte;
        uint8_t new_byte;
};

struct data01{
        uint32_t offset;
        uint16_t orig_word;
        uint16_t new_word;
};

int main(int argc, char** argv){
        if(argc != 4){
                errx(1, "ERROR: Invalid number of arguments!");
        }

        int fd1, fd2, fd3;
        if((fd1=open(argv[1], O_RDONLY)) == -1){
                err(2, "ERROR: Could not open %s file for reading!", argv[1]);
        }
        if((fd2=open(argv[2], O_RDONLY)) == -1){
                err(2, "ERROR: Could not open %s file for reading!", argv[2]);
        }
        if((fd3=open(argv[3], O_WRONLY | O_CREAT, 0766)) == -1){
                err(3, "ERROR: Could not open %s file for writing!", argv[3]);
        }

        int bytes_count;
        struct header h;

        if((bytes_count=read(fd1, &h, sizeof(h))) == -1){
                err(4, "ERROR: Could not read from %s file!", argv[1]);
        }
        if(h.magic != 0xEFBEADDE || h.header_version != 0x01){
                errx(5, "ERROR: Invalid magic number or header version in %s file", argv[1]);
        }

        struct data00 d;
        struct data01 d1;
        if(h.data_version == 0x00){
                uint8_t buf;
                while((bytes_count=read(fd2, &buf, sizeof(buf))) > 0){
                        if(write(fd3, &buf, sizeof(buf)) == -1){
                                err(7, "ERROR: Could not write to %s file!", argv[3]);
                        }
                }
                if(bytes_count == -1){
                        err(4, "ERROR: Could not read from %s file!", argv[2]);
                }
                for(uint16_t i = 0; i < h.count; i++){
                    if(read(fd1, &d, sizeof(d)) == -1){
                        err(4, "ERROR: Could not read from %s file!", argv[1]);
                        }

                        if(lseek(fd3, d.offset, SEEK_SET) == -1){
                                err(6, "ERROR: Could not lseek in %s file!", argv[3]);
                        }
                        if(read(fd3, &buf, sizeof(buf)) == -1){
                                err(4, "ERROR: Could not read from %s file!", argv[3]);
                        }

                        if(d.orig_byte == &buf){
                                if(lseek(fd3, d.offset, SEEK_SET) == -1){
                                        err(6, "ERROR: Could not lseek in %s file", argv[3]);
                                }
                                if(write(fd3, &d.new_byte, sizeof(d.new_byte)) == -1){
                                        err(7, "ERROR: Could not write to %s file!", argv[3]);
                                }
                        }
                }
        }
        else if(h.data_version == 0x01){
                uint16_t buf;
                while((bytes_count=read(fd2, &buf, sizeof(buf))) > 0){
                        if(write(fd3, &buf, sizeof(buf)) == -1){
                            err(7, "ERROR: Could not write to %s file!", argv[3]);
                        }
                }
                if(bytes_count == -1){
                        err(4, "ERROR: Could not read from %s file!", argv[2]);
                }
                for(uint16_t i = 0; i < h.count; i++){
                    if(read(fd1, &d1, sizeof(d1)) == -1){
                         err(4, "ERROR: Could not read from %s file!", argv[1]);
                    }
                    if(lseek(fd3, d1.offset, SEEK_SET) == -1){
                        err(6, "ERROR: Could not lseek in %s file!", argv[3]);
                    }
                    if(read(fd3, &buf, sizeof(buf)) == -1){
                         err(4, "ERROR: Could not read from %s file!", argv[3]);
                    }
                    if(d1.orig_word == &buf){
                        if(lseek(fd3, d1.offset, SEEK_SET) == -1){
                             err(6, "ERROR: Could not lseek in %s file", argv[3]);
                        }
                        if(write(fd3, &d1.new_word, sizeof(d1.new_word)) == -1){
                            err(7, "ERROR: Could not write to %s file!", argv[3]);
                        }
                    }
                }
            }

    close(fd1);
    close(fd2);
    close(fd3);
}
```

* Напишете програма на C, която по подадено име на (текстови) файл като параметър,
извежда съдържанието на файла сортирано, чрез употреба на външните програми cat и sort през pipe().

```c
#include <stdint.h>
#include <err.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>

int main(int argc, char** argv){
        if(argc != 2){
                errx(1, "ERROR: Invalid number of arguments!");
        }
        int pfd[2];
        if(pipe(pfd) < 0){
                err(2, "ERROR: Could not pipe");
        }
        pid_t pid = fork();
        if( pid == -1){
                err(3, "ERROR: Could not fork!");
        }
        if(pid == 0){
                close(pfd[0]);
                if(dup2(pfd[1], 1) == -1){
                        err(4, "ERROR: Could not dup!");
                }
                if(execlp("cat", "cat", argv[1], (char*)NULL) == -1){
                        err(5, "ERROR: Could not exec cat!");
                }
        }
        else {
                close(pfd[1]);
                int status;
                if(wait(&status) == -1){
                        err(6, "ERROR: Could not wait for child to finish!");
                }
                if(dup2(pfd[0], 0) == -1){
                        err(4, "ERROR: Could not dup!");
                }
                if(execlp("sort", "sort", (char*)NULL) == -1){
                        err(5, "ERROR: Could not exec sort!");
                }
        }
        exit(0);
}
```
