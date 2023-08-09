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
