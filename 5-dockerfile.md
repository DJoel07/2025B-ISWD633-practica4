# Dockerfile
Un Dockerfile es un archivo de texto plano que contiene una serie de instrucciones que Docker utiliza para construir una imagen de contenedor Docker. Este conjunto de instrucciones define cómo se debe configurar y construir una imagen de contenedor, incluyendo qué sistema operativo base usar, qué software instalar, qué archivos copiar en el contenedor y cómo configurar el entorno de ejecución.
 ![Dockerfile](img/relacion.PNG)


Tradicionalmente, el archivo docker no tiene extensión. Simplemente se denomina Dockerfile sin ningún tipo de extensión. Adicionalmente, los Dockerfiles pueden ser creados usando la extensión .dockerfile. Esto se utiliza cuando hay una necesidad de almacenar múltiples archivos docker en un solo directorio.
Las instrucciones en un Dockerfile son simples y están diseñadas para ser leídas y comprendidas fácilmente. 

### FROM 
Especifica la imagen base que se utilizará como punto de partida para construir la nueva imagen de Docker. Por ejemplo, FROM nginx:alpine significa que la nueva imagen se basará en la imagen oficial de Nginx que está etiquetada como "alpine". Al utilizar una imagen base existente, se heredan todas las configuraciones y software incluidos en esa imagen, lo que facilita la creación de nuevas imágenes sin tener que empezar desde cero.

### RUN
Ejecuta comandos en el contenedor durante el proceso de construcción de la imagen. Puedes usar esta instrucción para instalar software, configurar el entorno, o realizar cualquier otra tarea necesaria para preparar la imagen. Por ejemplo, RUN apt-get update && apt-get install -y <paquete> instalará un paquete utilizando el administrador de paquetes apt en una imagen basada en Ubuntu.

### COPY
Copia archivos o directorios desde la máquina host al sistema de archivos del contenedor. Se utiliza para agregar archivos, scripts u otros recursos necesarios para la aplicación en la imagen de contenedor. Por ejemplo, COPY ./mi-app /app copiará el directorio mi-app desde la máquina host al directorio /app en el contenedor.

### ADD
Copia archivos o directorios desde el sistema de archivos de la máquina host al sistema de archivos del contenedor. Aunque es similar a la instrucción COPY, ADD tiene algunas características adicionales, como la capacidad de descomprimir automáticamente archivos y de copiar archivos desde una URL remota.

### CMD 
Define el comando predeterminado que se ejecutará cuando se inicie el contenedor. Puede haber solo una instrucción CMD en un Dockerfile. Si se proporciona más de una, solo la última tendrá efecto. Por ejemplo, CMD ["nginx", "-g", "daemon off;"] ejecutará el servidor web Nginx en modo daemon cuando se inicie el contenedor.

### ENTRYPOINT
Configura el comando o el script que se ejecutará cuando se inicie un contenedor basado en la imagen. A diferencia de la instrucción CMD, que especifica el comando predeterminado que se ejecutará y que puede ser sobrescrito desde la línea de comandos al iniciar el contenedor, ENTRYPOINT establece un comando que no se puede sobrescribir fácilmente al iniciar el contenedor.
Es importante destacar que, si se proporciona un comando en la línea de comandos al iniciar el contenedor (por ejemplo, docker run <imagen> <comando>), este comando se agregará como argumentos al comando especificado en ENTRYPOINT, en lugar de sobrescribirlo. Esto permite que el contenedor sea más versátil y se adapte a diferentes necesidades de uso.

### EXPOSE
Informa a Docker que el contenedor escuchará en un puerto específico en tiempo de ejecución. No abre realmente el puerto, solo documenta que la aplicación dentro del contenedor puede usar dicho puerto. Por ejemplo, EXPOSE 80 expone el puerto 80 en el contenedor, lo que permite que se acceda a la aplicación que se esté ejecutando en ese puerto desde fuera del contenedor.

### ENV
Define variables de entorno dentro del contenedor. Las variables de entorno definidas con ENV estarán disponibles para cualquier proceso en el contenedor durante su ejecución. Por ejemplo, ENV MYSQL_ROOT_PASSWORD=password define una variable de entorno llamada MYSQL_ROOT_PASSWORD con el valor password.

### VOLUME 
Esta instrucción se se utiliza para definir un punto de montaje para volúmenes dentro del contenedor.

##  Para construir una imagen de Docker a partir de un Dockerfile
```
docker build -t <nombre imagen>:<tag> .
```
- **-t** esta opción se utiliza para etiquetar la imagen que se está construyendo con un nombre y una versión.
- <nombre_imagen>:<tag> por ejemplo: myapp:1.0
- **.** este punto indica al comando docker build que busque el Dockerfile en el directorio actual, es decir especifica la ubicación del contexto de la construcción que incluye el Dockerfile y cualquier otro archivo necesario para la construcción de la imagen.

## Ejemplo
### Colocar las siguientes instrucciones en un Dockerfile, 
![Dockerfile](img/Dockerfile.PNG)

- apachectl: Es el script de control para el servidor web Apache. Se utiliza para iniciar, detener y controlar el servidor web.
- -D FOREGROUND: Esta opción le dice a Apache que se ejecute en primer plano. Por defecto, Apache se ejecuta como un servicio en segundo plano. Sin embargo, en un contenedor Docker, es preferible que el proceso principal (en este caso, Apache) se ejecute en primer plano para que Docker pueda monitorear el estado del proceso. Si Apache se ejecutara en segundo plano, Docker no podría saber si el servidor web está funcionando correctamente o no.

 
### Ejecutar el archivo Dockerfile y construir una imagen en la versión 1.0
No olvides verificar en qué directorio se encuentra el archivo Dockerfile
```

```

**¿Cuántos pasos se han ejecutado?**
Se han ejecutado 7 pasos, que corresponden a las siguientes instrucciones del Dockerfile:

FROM centos:7 - [1/5] (CACHED - reutilizado de caché)
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g'... - [2/5] (0.3s)
RUN yum -y update - [3/5] (36.8s)
RUN yum -y install httpd - [4/5] (5.9s)
COPY ./web /var/www/html - [5/5] (0.1s)
EXPOSE 80 (implícito, no aparece en el output pero está en el Dockerfile)
CMD ["apachectl", "-D", "FOREGROUND"] (implícito, no aparece en el output pero está en el Dockerfile)

### Inspeccionar la imagen creada
<img width="978" height="516" alt="image" src="https://github.com/user-attachments/assets/68ffe257-4403-4e52-9a2a-08485c098cc0" />
<img width="951" height="728" alt="image" src="https://github.com/user-attachments/assets/ed0bdd11-17e3-4b16-a18e-bf666e9ca51a" />
<img width="957" height="798" alt="image" src="https://github.com/user-attachments/assets/4bf8c1ac-2333-430f-a326-b56e6981b608" />




**Modificar el archivo index.html para incluir su nombre y luego crear una nueva versión de la imagen anterior**
**¿Cuántos pasos se han ejecutado? ¿Observa algo diferente en la creación de la imagen**
Se han ejecutado los mismos 7 pasos que en la versión 1.0. Sin embargo, se observan diferencias significativas en el proceso de construcción:

Diferencias observadas:

Uso de Caché (CACHED):

[1/5] FROM centos:7 → CACHED (0.0s)
[2/5] RUN sed -i 's|#baseurl=...' → CACHED (0.0s)
[3/5] RUN yum -y update → CACHED (0.0s)
[4/5] RUN yum -y install httpd → CACHED (0.0s)
Paso reconstruido:

[5/5] COPY ./web /var/www/html → Se ejecutó nuevamente (0.0s)
Este paso se reconstruyó porque Docker detectó que el contenido del archivo index.html dentro del directorio web había cambiado.
Tiempo de construcción:

Versión 1.0: 57.6 segundos
Versión 2.0: 1.4 segundos
Diferencia: La construcción fue aproximadamente 41 veces más rápida gracias al mecanismo de caché de Docker.
Mecanismo de Caché de Docker:

Docker reutilizó las capas de los pasos 1 a 4 que no habían cambiado (imagen base, configuración de repositorios, actualización del sistema e instalación de Apache).
Solo reconstruyó el paso 5 (COPY) y los pasos subsiguientes porque el contenido de los archivos copiados cambió.
Esto demuestra la eficiencia del sistema de capas de Docker: solo se reconstruye lo que realmente cambió.
Transferencia de contexto:

Versión 1.0: transferring context: 59B
Versión 2.0: transferring context: 316B
El tamaño aumentó porque modificaste el archivo index.html, haciéndolo más grande al agregar tu nombre.

## Mecanismo de caché
Docker usa un mecanismo de caché cuando crea imágenes para acelerar el proceso de construcción y evitar la repetición de pasos que no han cambiado. Cada instrucción en un Dockerfile crea una capa en la imagen final. Docker intenta reutilizar las capas de una construcción anterior si no han cambiado, lo que reduce significativamente el tiempo de construcción.

- Instrucción FROM: Si la imagen base ya está en el sistema, Docker la reutiliza.
- Instrucciones de configuración (ENV, RUN, COPY, etc.): Docker verifica si alguna instrucción ha cambiado. Si no, reutiliza la capa correspondiente de la caché.
- Instrucción COPY y ADD: Si los archivos copiados no han cambiado, Docker reutiliza la capa de caché correspondiente.
![mapeo](img/dockerfile-cache.PNG)

### Crear un contenedor a partir de las imagen creada, mapear todos los puertos
```
docker run -d --name contenedor-apache -P mi-apache:2.0
```

### ¿Con que puerto host se está realizando el mapeo?
El contenedor está mapeado al puerto 32768 del host, que redirige al puerto 80 del contenedor donde Apache está escuchando.
<img width="1213" height="90" alt="image" src="https://github.com/user-attachments/assets/23f62f7a-961a-4314-be2b-bffce1e9935c" />

**¿Qué es una imagen huérfana?**

Una imagen huérfana es una imagen de Docker que cumple con las siguientes características:

1. **No tiene nombre ni etiqueta (tag) asociada**: Aparece en la lista de imágenes como `<none>:<none>` tanto en el nombre del repositorio como en el tag.

2. **No está siendo utilizada por ningún contenedor**: No hay contenedores activos o detenidos que dependan de esta imagen.

3. **Se genera principalmente cuando**:
   - Se construye una nueva versión de una imagen con el **mismo nombre y tag** que una imagen existente. La versión anterior pierde su etiqueta y se convierte en huérfana.
   - Un proceso de construcción de imagen **falla** o se **interrumpe** a mitad de camino.
   - Se **eliminan manualmente los tags** de una imagen sin eliminar la imagen completa.
   - Durante el desarrollo, al reconstruir imágenes repetidamente con el mismo nombre.

4. **Problema que generan**:
   - **Ocupan espacio en disco innecesariamente**, ya que contienen capas de imágenes que ya no se utilizan.
   - Pueden **acumularse con el tiempo** si no se realiza un mantenimiento regular del sistema Docker.


### Identificar imágenes huérfanas
```
docker images -f "dangling=true"
```

### Listar los IDS de las imágenes huérfanas
```
docker images -f "dangling=true" -q
```

### Eliminar imágenes huérfanas
Este comando eliminará todas las imágenes que no estén asociadas a ningún contenedor en ejecución. Antes de ejecutarlo, asegúrate de revisar las imágenes que serán eliminadas para evitar la pérdida de imágenes importantes. 
```
docker image prune
```

### Para Ejecutar un archivo Dockerfile que tiene otro nombre
```
docker build -t <nombre imagen>:<tag> -f <ruta y nombre del Dockerfile> .
```

## Por ejemplo
docker build -t imagen:1.0 -f Dockerfile-custom .



