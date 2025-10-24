# Limitar uso de procesador
Limitar la cantidad de núcleos de CPU:
```
--cpus=<número de núcleos>
```

Asignar núcleos de CPU específicos:
```
--cpuset-cpus=<lista de núcleos>
```

**¿Como saber el numero de procesadores virtuales que tiene una máquina?**

En **Windows**, puedes usar los siguientes métodos:

1. **Usando PowerShell:**
```powershell
Get-WmiObject -Class Win32_Processor | Select-Object NumberOfLogicalProcessors
```

O de forma más simple:
```powershell
(Get-WmiObject Win32_ComputerSystem).NumberOfLogicalProcessors
```

2. **Usando el Administrador de tareas:**
   - Presiona `Ctrl + Shift + Esc`
   - Ve a la pestaña "Rendimiento"
   - Selecciona "CPU"
   - En la parte inferior verás "Procesadores lógicos"

3. **Usando la variable de entorno:**
```cmd
echo %NUMBER_OF_PROCESSORS%
```

4. **Usando WMIC (Windows Management Instrumentation Command):**
```cmd
wmic cpu get NumberOfLogicalProcessors
```


## Ejemplos
_Puedes copiar y ejecutar directamente cada uno de los comandos_

Limitar el uso de CPU a 1.5 núcleos
```
docker run -d --name server-nginx --cpus="1.5" nginx:alpine
```

Restringir el contenedor para que use los núcleos de CPU 0 a 2:
```
docker run -d --name server-nginx --cpuset-cpus="0-2" nginx:alpine
```

Restringir el contenedor para que use los núcleos de CPU 1 y 3:
```
docker run -d --name server-nginx --cpuset-cpus="1,3" nginx:alpine
```
