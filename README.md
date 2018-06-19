# Please
#!/bin/bash
# Script de surveillance de la charge système de la base de donnée de l’ERP

mail1="doudou.gueye@engie.com"
mail2="fabien.remy@engie.com"
sujet="Charge CPU > 90% sur $HOSTNAME" 
compteur=0
while 
	true
	do
# On attend 30 secondes entre chaque check 
	sleep 30
cpu_cores='cat /proc/cpuinfo | grep 'processor' | wc -l' load_1min='cat /proc/loadavg | awk '{print $1}''
charge='echo $(($(echo $load_1min | awk '{print 100 * $1}') / $cpu_cores))'
# si la charge est supérieure ou égale à 90 %
if [ $charge -ge 90 ]
	then
# On incrémente alors le compteur de 1
	 compteur=$((compteur+1))

# Si le compteur arrive à 3
 if [ $compteur -eq 3 ]
	then
	top="$(ps -eo %cpu,pid,comm | sort -nr | head -3)" 
	date='date '+%d-%m-%Y %H:%M:%S''

# Alors on envoie les mails d'alerte à Doudou GUEYE et Fabien REMY puis on remet le compteur à 0 
	echo -e "$sujet\n\n" "$top" | mail1 -s "$date : $sujet" $mail1
	echo -e "$sujet\n\n" "$top" | mail2 -s "$date : $sujet" $mail2
	compteur=0 
	fi
else

# Si la charge est inférieure ou égale à 90 % alors le compteur est à 0 
	compteur=0
	fi 
done
