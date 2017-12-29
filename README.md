# rclone_boot_enigma

A continuación se va a explicar una forma de realizar el automontaje de un remote con 
rclone en el arranque de un sistema basado en Enigma2.

Para comenzar, aclarar un detalle importante. Las tareas programadas en cron '@reboot' no 
funcionan en los sistemas Enigma. Es por ello que si queremos ejecutar algún programa en el 
arranque, tenemos que hacer uso de un script que se incluye en el proceso de arranque del sistema.

A posteriori, subiré mi script programado para el automontaje de unidades en rclone. Sería otra 
alternativa para realizar el automontaje, ya que el script comprueba si el/los remote/s 
declarados, se encuentran montados en el sistema, y de no ser así, los vuelve a montar. Pero esta 
opción, aún tengo que testearla, y es por ello que de momento, solo va a ser una pequeña 'guía' 
(por llamarlo de alguna manera), para poder programar rclone en el arranque.

Los pasos a seguir (recomiendo realizar los pasos como root):

1. Realizar la configuración de los remotes tal como hacemos siempre (en caso de no haberlo 
hecho).
2. Cuando tengamos los remotes configurados, lo siguiente es copiar el fichero de configuración 
a un lugar accesible para cualquier usuario del sistema. Por ejemplo, voy a crear un directorio 
llamado '/remotes' y dentro del mismo voy a copiar la configuración de rclone (el lugar dependerá 
de que usuario hayamos escogido para realizar la configuración de los remotes); por lo 
general '/home/nombre_usuario/.config/rclone/rclone.conf'. Así y a modo ejemplo, copiaría el 
fichero: 'cp /home/nombre_usuario/.config/rclone/rclone.conf /remotes'
3. Nos aseguramos de que el directorio sea accesible por todos los usuarios, y el fichero 
rclone.conf también.
4. Verificado lo anterior, editamos el fichero con el editor que creamos oportuno: 
'/usr/bin/enigma2_pre_start.sh'
5. Añadimos en el fichero: 'sleep 120'
6. A continuación la cadena de montaje de nuestro remote. Por ejemplo: '/usr/bin/rclone --config 
"/remotes/rclone.conf" --tpslimit 6 mount --allow-other --read-only remote1:/ /mnt/remote1 &'
7. Guardamos el fichero y listo. Ya podremos realizar el reinicio del sistema para comprobar si se 
realiza el montaje.

ACLARACIONES:

- El método no es el más pragmático, ya que quien ejecuta la orden es el proceso init, y no un 
usuario del sistema, es por ello que es necesario proveer la configuración de los remotes mediante 
el flag --config tal como se aprecia en el ejemplo.
- De la misma forma, las variables de entorno aún no están en marcha, y es por ello, que hay que 
indicar donde se encuentra el binario rclone para su ejecución.
- Importante el uso de sleep, porque hasta que en enlace de red no se habilita, no hay acceso a 
internet, por tanto el intento de montaje fallaría. Es por ello que he dado un margen de 120 
segundos desde el arranque del script que hemos editado. Este tiempo puede variar dependiendo del 
equipo, en mi caso 120 segundos fueron más que suficientes para que funcionara.
- IMPRESCINDIBLE el uso de '&' en el comando de montaje, para que el montaje no se apropie del 
proceso init, y se ejecute en segundo plano. 

Cuando tenga testeado el script de automontaje propio, se podrá realizar la programación del 
automontaje mediante cron, y será mucho más sencillo. En cuanto lo tenga, modificaré este 
minituto, y dejaré las dos opciones explicadas.


