# Redes Inalámbricas

Proyecto académico de Conceptos de Programación desarrollado en Java. Su objetivo es analizar conexiones de dispositivos a puntos de acceso inalámbricos (AP), contar las direcciones MAC únicas por AP y generar un reporte ordenado por cantidad de dispositivos.

**Estado actual:** funcionan la generación de datos de prueba y el módulo de conteo. La lectura, la clasificación, el reporte y la integración completa están pendientes.

## Funcionalidades y responsabilidades

| Módulo | Clases principales | Estado |
| --- | --- | --- |
| Generación de datos | `GenerateInfoFiles`, `GeneradorAP`, `GeneradorConexiones` | Implementado y ejecutable |
| Modelos y lectura | `AccesPoint`, `Conexion`, `LectorArchivos` | Pendiente; clases vacías |
| Conteo de dispositivos únicos | `AnalizadorRed` | Implementado; falta conectarlo al lector |
| Clasificación y reporte | `ClasificadorAP`, `GeneradorReporte` | Pendiente; clases vacías |
| Integración e interfaz | `main`, `VentanaPrincipal` | Pendiente; clases vacías |

El flujo previsto es: generar archivos → leer datos → contar MAC únicas por AP → ordenar resultados → generar `saturacion.csv`.

## Requisitos

- JDK 11 o superior, con `java` y `javac` disponibles en la terminal.
- Git, para clonar y colaborar en el repositorio.
- Opcional: Visual Studio Code con soporte para Java.
- Actualmente no se requieren bibliotecas externas ni Maven o Gradle.

## Ejecución

### Preparar un clon nuevo

```text
git clone https://github.com/juanma-h/Redes-Inalambricas.git
cd Redes-Inalambricas
java -version
javac -version
```

Ambos comandos de Java deben indicar una versión 11 o superior. Se necesita el JDK (incluye el compilador), no solamente el entorno de ejecución.

El clon contiene el código fuente y la documentación. `bin/` y los archivos de datos se crean al compilar y ejecutar; no es necesario descargarlos ni copiarlos de otro compañero. La configuración compartida de VS Code usa rutas relativas al proyecto.

### Desde Visual Studio Code

1. Abrir la carpeta del repositorio.
2. Abrir `src/GenerateInfoFiles.java`.
3. Ejecutar su método `main` mediante **Run Java**.
4. Consultar las rutas de los archivos generados que aparecen en la consola.

El punto de entrada disponible es `GenerateInfoFiles`. El archivo `src/main.java` todavía no contiene un método ejecutable.

### Desde PowerShell

Ejecutar estos comandos desde la raíz del repositorio:

```powershell
New-Item -ItemType Directory -Force -Path bin | Out-Null
javac --release 11 -encoding UTF-8 -d bin src/files/GeneradorAP.java src/files/GeneradorConexiones.java src/GenerateInfoFiles.java
java -cp bin GenerateInfoFiles
```

Si la compilación termina correctamente, la ejecución crea `aps.csv` y `conexiones.txt` en la carpeta de trabajo del proceso. Con los comandos anteriores, esa carpeta es la raíz del repositorio.

**Cada ejecución reemplaza el contenido anterior de ambos archivos.** El generador no ejecuta el análisis ni produce todavía `saturacion.csv`.

### Desde Linux o macOS

```sh
mkdir -p bin
javac --release 11 -encoding UTF-8 -d bin src/files/GeneradorAP.java src/files/GeneradorConexiones.java src/GenerateInfoFiles.java
java -cp bin GenerateInfoFiles
```

### Compilar todos los módulos en PowerShell

```powershell
New-Item -ItemType Directory -Force -Path bin | Out-Null
$fuentes = Get-ChildItem -Path src -Recurse -Filter *.java | ForEach-Object FullName
javac --release 11 -encoding UTF-8 -d bin $fuentes
```

La compilación completa comprueba los fuentes actuales, pero no sustituye las pruebas de integración de los módulos pendientes.

### Problemas frecuentes

| Problema | Qué revisar |
| --- | --- |
| `javac` no se reconoce | Instalar un JDK y añadir su carpeta `bin` al `PATH`; volver a abrir la terminal. |
| `Could not find or load main class GenerateInfoFiles` | Compilar primero sin errores y ejecutar `java -cp bin GenerateInfoFiles` desde la raíz. |
| No aparece `aps.csv` | Revisar la ruta absoluta impresa en consola y los posibles errores de escritura. |
| Tildes incorrectas | Guardar los fuentes como UTF-8 y compilar con `-encoding UTF-8`. |
| Se ejecuta una versión antigua | Volver a compilar los fuentes; cada integrante genera sus propios `.class`. |

## Datos de prueba

La configuración predeterminada genera **8 AP y 50 conexiones**. Para cambiar estas cantidades, editar `CANTIDAD_AP` y `CANTIDAD_CONEXIONES` en `src/GenerateInfoFiles.java`. Las ubicaciones disponibles se definen en ese mismo archivo.

Los datos se seleccionan aleatoriamente. Cada quinta conexión repite la anterior, incluyendo AP y MAC, para probar la detección de duplicados.

Los archivos de entrada utilizan UTF-8, un registro por línea, campos separados por `;` y ningún encabezado.

### `aps.csv`

Formato: `identificador;ubicacion`.

```text
AP01;Biblioteca
AP02;Laboratorio
```

### `conexiones.txt`

Formato: `identificadorAP;direccionMAC`. Cada AP utilizado procede del catálogo generado.

```text
AP01;00:1A:2B:3C:4D:5E
AP01;00:1A:2B:3C:4D:5E
AP02;10:2C:3D:4E:5F:60
```

En este ejemplo, el conteo esperado es un dispositivo único para `AP01` y uno para `AP02`. Las dos primeras líneas representan el mismo dispositivo en el mismo AP.

### `saturacion.csv` — salida pendiente

Formato previsto: `identificador;ubicacion;cantidadDispositivosUnicos`, ordenado de mayor a menor cantidad.

```text
AP02;Laboratorio;12
AP01;Biblioteca;8
```

Estos registros ilustran el formato esperado; el módulo que los genera aún no está implementado.

## Estructura del proyecto

```text
src/
├── GenerateInfoFiles.java       Coordinación de la generación
├── main.java                    Integración general pendiente
├── files/                       Generadores, lector y reporte
├── model/                       Modelos de AP y conexión
├── services/                    Análisis y clasificación
└── interfaz/                    Interfaz gráfica pendiente
bin/                            Clases compiladas locales (ignorado)
.vscode/settings.json           Configuración Java de VS Code
.gitignore                      Exclusiones de archivos locales
README.md                       Instalación, ejecución y estado
```

La clase del modelo se llama actualmente `AccesPoint`. Conviene acordar su cambio a `AccessPoint` antes de integrarla con los demás módulos.

## Pendientes principales

- Implementar los modelos y la lectura con validación de líneas.
- Conectar las conexiones leídas con `AnalizadorRed` e incluir los AP sin conexiones con conteo cero.
- Implementar el ordenamiento y la escritura de `saturacion.csv`.
- Integrar y probar el proceso completo desde el punto de entrada general.
- Añadir pruebas automáticas y completar la documentación conforme avance el proyecto.

## Colaboración y archivos versionados

Se comparten los fuentes, la documentación y la configuración portable de VS Code. `.gitignore` excluye compilados, directorios de construcción, datos generados en la raíz, temporales, configuración personal de editores y archivos `.env` locales.

Las exclusiones de datos se limitan a `/aps.csv`, `/conexiones.txt` y `/saturacion.csv`: no se ignoran todos los CSV o TXT, para permitir futuros ejemplos y pruebas. Los JAR tampoco se ignoran globalmente; actualmente no hay dependencias externas.

Antes de aportar cambios:

1. Crear una rama para la tarea desde una versión actualizada de `main`.
2. Compilar y comprobar el módulo modificado.
3. Actualizar el README si cambia el estado o la ejecución.
4. Revisar `git status` y `git diff`; agregar explícitamente los archivos del aporte.
5. Abrir un pull request indicando qué funciona y cómo se verificó.

Los `.class` previamente versionados se retiran del índice de Git como parte de la limpieza. Al recibir ese cambio, basta con volver a compilar para regenerarlos. `.gitignore` no elimina archivos del disco ni borra su historial anterior.
