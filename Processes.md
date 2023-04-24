# Exercises on processes

* Намерете командите на 10-те най-стари процеси в системата.
```shell
ps -eo etimes,comm | tail -n +2 | sort -n -k1 | tail -n 10 | tr -s ' ' | cut -d ' ' -f 3
```

* Намерете PID и командата на процеса, който заема най-много виртуална памет в системата.
```shell
ps -eo vsz,pid,comm | tail -n +2 | tr -s ' ' | sort -n -k 1 | tail -n 1 | cut -d ' ' -f 2-
```

* Изведете командата на най-стария процес
```shell
ps -eo etimes,comm | tail -n +2 | tr -s ' ' | sort -n -k1 | tail -n 1 | cut -d ' ' -f 3
```

* Намерете колко физическа памет заемат всички процеси на потребителската група root.
```shell
ps -eo group,drs | tail -n +2 | sort -k1 | tr -s ' ' | awk '/root/ {sum=sum+$2} END {print sum}'
```
