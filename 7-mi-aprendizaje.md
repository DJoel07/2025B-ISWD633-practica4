# Mi Aprendizaje
# Dorian Joel Alban Lucas

## Conocimientos Adquiridos

### 1. Limitación de Recursos en Docker

Antes de realizar esta práctica, no comprendía la importancia de limitar los recursos en contenedores Docker. Ahora entiendo que:

- **Memoria RAM y Swap**: Aprendí a usar los parámetros `--memory` y `--memory-swap` para controlar el consumo de memoria de los contenedores. Esto es crucial en entornos de producción donde múltiples contenedores comparten recursos limitados del host.

- **CPU**: Descubrí cómo limitar el uso de procesadores mediante `--cpus` y `--cpuset-cpus`, lo cual permite una distribución equitativa de los recursos computacionales y evita que un contenedor monopolice todo el procesamiento.

- **Importancia práctica**: Comprendí que estas limitaciones no solo optimizan el rendimiento, sino que también previenen problemas de seguridad donde un contenedor comprometido podría afectar todo el sistema.

### 2. Healthchecks

Antes no sabía cómo Docker podía monitorear automáticamente el estado de salud de los contenedores. Ahora entiendo:

- Cómo configurar comandos específicos para verificar si un servicio está funcionando correctamente
- La diferencia entre un contenedor que está "corriendo" y uno que está "saludable"
- La utilidad del parámetro `|| exit 1` para asegurar que los errores se reporten correctamente

### 3. Dockerfile y Construcción de Imágenes

Este fue uno de los aprendizajes más significativos:

- **Instrucciones básicas**: Aprendí el propósito de cada instrucción (FROM, RUN, COPY, CMD, EXPOSE, etc.) y cómo estructurar un Dockerfile de manera lógica.

- **Mecanismo de caché**: Descubrí que Docker usa un sistema de capas que permite reutilizar pasos que no han cambiado. Esto fue evidente cuando construí la versión 2.0 de mi imagen, que tomó solo 1.4 segundos vs 57.6 segundos de la versión 1.0, porque reutilizó las capas cacheadas.

- **Optimización**: Entendí que el orden de las instrucciones importa. Las instrucciones que cambian con menos frecuencia deben ir primero para aprovechar mejor el caché.

### 4. Políticas de Reinicio

Aprendí sobre las diferentes estrategias para manejar contenedores que se detienen:

- **no**: Política por defecto, útil para contenedores que ejecutan tareas únicas
- **always**: Para servicios críticos que deben estar siempre disponibles
- **unless-stopped**: Similar a always pero respeta las detenciones manuales
- **on-failure**: Ideal para procesos que pueden fallar ocasionalmente y necesitan reiniciarse automáticamente

Esto me hizo reflexionar sobre la importancia de diseñar aplicaciones resilientes y cómo Docker facilita su gestión.

### 5. Imágenes Huérfanas

Aprendí qué son las imágenes huérfanas (`<none>:<none>`) y cómo se generan al reconstruir imágenes con el mismo tag. También descubrí comandos para identificarlas y eliminarlas (`docker image prune`), lo cual es importante para mantener limpio el sistema.

## Problema Enfrentado y Solución

### Problema: CentOS 7 End of Life (EOL)

Al intentar construir la imagen con el Dockerfile proporcionado, encontré el siguiente error:

```
Could not retrieve mirrorlist http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=container error was
14: curl#6 - "Could not resolve host: mirrorlist.centos.org; Unknown error"
```

**Análisis del problema:**

Investigué y descubrí que CentOS 7 llegó al final de su ciclo de vida (EOL) el 30 de junio de 2024. Esto significa que los repositorios oficiales en `mirrorlist.centos.org` y `mirror.centos.org` ya no están disponibles, por lo que los comandos `yum update` y `yum install` fallaban al no poder descargar los paquetes necesarios.

**Solución implementada:**

Modifiqué el Dockerfile para redirigir las solicitudes de paquetes a los repositorios archivados (vault) de CentOS, que mantienen las versiones EOL:

```dockerfile
RUN sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*.repo && \
    sed -i 's|mirrorlist=|#mirrorlist=|g' /etc/yum.repos.d/CentOS-*.repo
```

Este comando hace dos cosas:
1. Descomenta y cambia las URLs de `mirror.centos.org` a `vault.centos.org`
2. Comenta las líneas de mirrorlist que ya no funcionan

**Aprendizaje de esta experiencia:**

- Aprendí sobre el ciclo de vida de las distribuciones Linux y la importancia de usar versiones con soporte activo en producción
- Desarrollé habilidades de troubleshooting al leer logs de error y buscar soluciones
- Comprendí la importancia de los repositorios archivados para mantener compatibilidad con sistemas legacy
- Descubrí alternativas como usar Ubuntu en lugar de CentOS para evitar este tipo de problemas en el futuro

