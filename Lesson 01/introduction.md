# Lesson 1: bash scripting

Bash es un lenguaje de programación y, al mismo tiempo, un intérprete de comandos. Su principal característica es que está pensado para interactuar directamente con el sistema operativo y con las herramientas que este proporciona.

En otros lenguajes, como Python o PHP, es habitual incorporar bibliotecas que agregan funciones para resolver determinadas tareas. Por ejemplo, una biblioteca puede permitir trabajar con archivos, conectarse a una base de datos, realizar solicitudes de red o procesar información.

En Bash, gran parte de esas funciones ya están disponibles a través de las propias herramientas del sistema operativo. Comandos como `grep`, `find`, `sed`, `awk`, `curl`, `ps` o `ip` pueden verse, en cierto sentido, como las herramientas con las que Bash construye sus soluciones.

De esta forma, Bash permite combinar programas pequeños y especializados, pasando información de unos a otros y controlando su ejecución mediante estructuras propias del lenguaje.

Por este motivo, aprender Bash no consiste solamente en aprender su sintaxis. También implica conocer progresivamente las herramientas disponibles en un sistema GNU/Linux y entender cómo pueden trabajar juntas.

### Primer contacto con las herramientas del sistema

Podemos comenzar ejecutando algunos comandos simples para obtener información sobre distintos aspectos del sistema operativo.

Por ejemplo:

```bash
uname -a
```

Muestra información sobre el sistema y el kernel.

```bash
free -h
```

Muestra información sobre el uso de la memoria.

```bash
df -h
```

Muestra información sobre el espacio utilizado y disponible en los sistemas de archivos.

Podemos ejecutar cada comando de forma independiente, pero Bash también permite escribir varios comandos en una misma línea separándolos mediante `;`.

Por ejemplo:

```bash
uname -a ; free -h ; df -h
```

En este caso, Bash ejecutará los tres comandos, uno después del otro.

Cuando abrimos una terminal y trabajamos desde la línea de comandos, normalmente estamos interactuando con una instancia de Bash que se encuentra en ejecución. Bash actúa como intérprete: recibe lo que escribimos, interpreta la instrucción y se encarga de ejecutar los comandos correspondientes. Esto ocurre tanto cuando ejecutamos un único comando como cuando combinamos varios comandos en una misma línea.



### Nuestro primer script

Un script de Bash es, en esencia, un archivo que contiene una secuencia de comandos ordenados según la tarea que queremos realizar.

Cuando ejecutamos ese archivo, invocamos a un intérprete Bash que irá leyendo las instrucciones y ejecutándolas una por una, en el orden en que aparecen.

Conceptualmente, no es muy diferente de lo que hicimos antes al escribir varios comandos separados por `;`:

```bash
uname -a ; free -h ; df -h
```

En un script podemos escribir esos mismos comandos uno debajo del otro y guardar la secuencia para ejecutarla cuando la necesitemos.

El archivo de este ejemplo será:

```text
/home/user/commands.sh
```

Su contenido será:

```bash
#!/bin/bash

# Archivo: /home/user/commands.sh
# reemplazar 'user' con el usuario correspondiente

# Mostrar información del sistema y del kernel
uname -a

# Mostrar información sobre el uso de memoria
free -h

# Mostrar información sobre el espacio en los sistemas de archivos
df -h
```

La primera línea:

```bash
#!/bin/bash
```

indica qué intérprete debe utilizarse para ejecutar el contenido del archivo.

De esta manera, Bash recorre el script y ejecuta cada comando uno después del otro.

### Formas de ejecutar un script

Una forma de ejecutar nuestro script es invocar explícitamente un nuevo intérprete de Bash y pasarle el archivo que debe procesar:

```bash
bash /home/user/commands.sh
```

En este caso, se inicia una nueva instancia de Bash, que abre el archivo, recorre sus instrucciones y las ejecuta una por una.

Otra posibilidad es darle permiso de ejecución al archivo:

```bash
chmod +x /home/user/commands.sh
```

y ejecutarlo directamente:

```bash
/home/user/commands.sh
```

O si estamos posicionados en el mismo directorio que el archivo podemos usar la forma de ruta relativa:

```
./commands.sh
```

En este caso, el sistema utiliza la primera línea del script:

```bash
#!/bin/bash
```

para determinar qué intérprete debe ejecutar el archivo.

Es importante notar que esta segunda forma tampoco ejecuta el script dentro del Bash que tenemos actualmente abierto: se inicia el intérprete indicado por el `#!`. Para ejecutarlo dentro del intérprete Bash actual se utilizaría `source`:

```bash
source /home/user/commands.sh
```

### Redirección de la salida

Bash permite redirigir la salida de un comando hacia un archivo utilizando los operadores `>` y `>>`.

El operador `>` guarda la salida en un archivo. Si el archivo ya existe, reemplaza su contenido.

```bash
uname -a > sistema.txt
```

El operador `>>` también guarda la salida en un archivo, pero agrega la nueva información al final sin borrar lo que ya contiene.

```bash
free -h >> sistema.txt
```

Podemos aplicar esto a nuestro script para guardar toda la información obtenida en un único archivo:

```bash
#!/bin/bash

# Archivo: /home/user/commands.sh

# Crear el archivo de informe e indicar qué usuario lo generó
# $USER es una variable de entorno que contiene tu nombre de usuario
echo "Informe generado por $USER" > /home/$USER/system-info.txt

# Agregar la fecha y hora de generación
# $HOME es una variable de entorno que contiene la ruta de tu directorio de usurio
date >> $HOME/system-info.txt

# Agregar información del sistema y del kernel
uname -a >> $HOME/system-info.txt

# Agregar información sobre el uso de memoria
free -h >> $HOME/system-info.txt

# Agregar información sobre los sistemas de archivos
df -h >> $HOME/system-info.txt

```

En este caso, `system-info.txt` se crea o se reemplaza al ejecutar el primer comando y los siguientes comandos agregan su salida al final del mismo archivo.

El contenido del archivo generado puede mostrarse en la terminal utilizando el comando `cat`:

```bash
cat /home/$USER/system-info.txt
```

`cat` lee el contenido del archivo y lo imprime directamente en la salida estándar.

### Salida estándar y salida de error

Cuando ejecutamos un comando, Bash distingue entre la salida normal del programa y los mensajes de error.

Podemos verlo con este ejemplo:

```bash
ls /home /nofile > ~/salida.txt
```

`/home` existe, por lo que su contenido se envía a `salida.txt` dentro de nuestro directorio personal. En cambio, `/nofile` no existe y el mensaje de error se sigue mostrando en la terminal.

Esto ocurre porque `>` redirige únicamente la salida estándar.

Los mensajes de error utilizan otro canal, identificado como `2`. Para redirigir solamente los errores podemos utilizar:

```bash
ls /home /nofile 2> ~/errores.txt
```

En este caso, la información correspondiente a `/home` se muestra normalmente en la terminal, mientras que el error producido por `/nofile` se guarda en `errores.txt`.

También podemos redirigir cada salida a un archivo diferente:

```bash
ls /home /nofile > ~/salida.txt 2> ~/errores.txt
```

De esta manera, la salida correcta queda almacenada en `~/salida.txt` y los mensajes de error en `~/errores.txt`.

También podemos enviar tanto la salida estándar como los mensajes de error al mismo archivo:

```bash
ls /home /nofile > ~/resultado.txt 2>&1
```

En este caso, `>` redirige la salida estándar hacia `resultado.txt` y `2>&1` indica que la salida de error debe enviarse al mismo destino que la salida estándar.

## Actividades

### Entorno

Para realizar las actividades de esta lección vamos a utilizar una máquina virtual con Debian o Ubuntu.

El objetivo será trabajar directamente desde una terminal Bash y aplicar sobre un mismo script los conceptos vistos hasta ahora: ejecución de comandos, uso de variables de entorno y redirección de la salida.

El script deberá utilizar las variables `$USER` y `$HOME`, que ya vimos anteriormente. `$USER` contiene el nombre del usuario actual y `$HOME` contiene la ruta de su directorio personal.

También utilizaremos los operadores de redirección `>`, `>>` y `2>`, para controlar dónde se almacenan la salida estándar y los mensajes de error producidos por los comandos.

### Actividad: automatizar la actualización del sistema

En Debian y Ubuntu, `apt` es una de las principales herramientas utilizadas para administrar paquetes de software. Permite consultar los repositorios configurados, instalar o eliminar paquetes y mantener actualizado el sistema.

Una actualización habitual puede incluir varias operaciones:

```bash
sudo apt update
sudo apt upgrade
sudo apt autoremove
sudo apt clean
```

`apt update` actualiza la información disponible sobre los paquetes y sus versiones. `apt upgrade` instala las actualizaciones disponibles. `apt autoremove` elimina dependencias que ya no son necesarias y `apt clean` elimina los paquetes descargados almacenados en la caché.

Algunas operaciones pueden solicitar confirmación antes de continuar. `apt` permite utilizar la opción `-y` para responder automáticamente de forma afirmativa:

```bash
sudo apt upgrade -y
```

Esto resulta especialmente útil cuando una operación forma parte de un script y queremos evitar que quede detenida esperando una respuesta del usuario.

La actividad consiste en crear un script que automatice este proceso de actualización. Además de ejecutar los comandos necesarios, nos interesa registrar qué sucede durante su ejecución.

Para ello deberás utilizar las redirecciones estudiadas anteriormente, separando la salida estándar de los mensajes de error y almacenando ambas en archivos dentro del directorio personal del usuario. También deberás utilizar las variables de entorno `$USER` y `$HOME` para identificar al usuario que ejecutó el proceso y determinar dónde guardar los archivos generados.

El objetivo no es solamente automatizar la actualización, sino también dejar un registro que permita revisar posteriormente qué ocurrió durante el proceso.

**Pruebas del script**

Una vez creado el script, no alcanza con comprobar que se ejecuta. También debemos verificar que las redirecciones funcionen correctamente y que podamos distinguir qué ocurrió durante el proceso.

Realizá distintas pruebas y observá qué información queda registrada en cada archivo.

1. Ejecutá el script con privilegios administrativos.

   Comprobá que la actualización pueda realizarse y revisá qué información quedó almacenada en el archivo de salida estándar y cuál en el archivo de errores.

2. Ejecutá el mismo script sin privilegios administrativos.

   Observá qué comandos fallan y verificá que esos mensajes queden registrados en el archivo destinado a la salida de error.

3. Ejecutá el script con conexión a Internet.

   Comprobá que `apt update` pueda acceder a los repositorios y revisá qué información genera durante el proceso.

4. Desconectá temporalmente la máquina virtual de la red y ejecutá nuevamente el script.

   Observá los errores producidos al intentar acceder a los repositorios y verificá que sean enviados al archivo correspondiente.

5. Finalmente, compará los archivos generados en cada prueba utilizando `cat`.

El objetivo de estas pruebas es comprobar que el script no solamente automatiza la actualización, sino que también permite documentar qué ocurrió durante una ejecución normal y qué ocurrió cuando apareció un problema.

### Actividad: búsqueda de archivos con `find`

El comando `find` permite buscar archivos y directorios a partir de diferentes criterios, como su nombre, tipo, ubicación o fecha de modificación.

La forma general de uso es:

```bash
find ruta criterios
```

Por ejemplo, para buscar dentro de `/etc` todos los archivos cuya extensión sea `.conf`:

```bash
find /etc -type f -name "*.conf"
```

`/etc` indica desde dónde comienza la búsqueda, `-type f` limita el resultado a archivos regulares y `-name "*.conf"` selecciona únicamente aquellos cuyo nombre termina en `.conf`.

También podemos buscar solamente los archivos que se encuentran directamente dentro de `/proc`:

```bash
find /proc -maxdepth 1 -type f
```

La opción `-maxdepth 1` evita que `find` continúe buscando dentro de los subdirectorios.

Otro criterio posible es la fecha de modificación. Para buscar archivos modificados durante los últimos siete días dentro de `/var/log`:

```bash
find /var/log -type f -mtime -7
```

`-mtime -7` selecciona archivos cuyo contenido fue modificado hace menos de siete días.

**Consigna**

Creá un script que realice las tres búsquedas anteriores de manera automática:

```bash
find /etc -type f -name "*.conf"

find /proc -maxdepth 1 -type f

find /var/log -type f -mtime -7
```

El script deberá guardar los resultados de las búsquedas en archivos dentro del directorio personal del usuario.

Utilizá las variables de entorno `$USER` y `$HOME` y los operadores de redirección estudiados anteriormente.

Separá la salida estándar de los mensajes de error, de manera que sea posible consultar posteriormente tanto los archivos encontrados como los problemas que se produjeron durante las búsquedas.

**Pruebas**

Una vez terminado el script, realizá diferentes ejecuciones para comprobar el funcionamiento de las redirecciones.

Primero ejecutalo normalmente, sin privilegios administrativos:

```bash
./find-files.sh
```

Revisá los archivos generados y observá si alguna de las búsquedas produce mensajes de error debido a directorios o archivos para los cuales el usuario no tiene permisos suficientes.

Después ejecutá el mismo script con privilegios administrativos:

```bash
sudo ./find-files.sh
```

Compará los resultados con la ejecución anterior.

Finalmente, utilizá `cat` para revisar tanto la salida estándar como los mensajes de error que quedaron registrados.

El objetivo de la actividad es comprobar cómo un mismo comando puede producir resultados diferentes dependiendo de los permisos del usuario y cómo las redirecciones permiten conservar por separado la información obtenida y los errores producidos durante la ejecución.