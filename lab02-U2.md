# SESION DE LABORATORIO N° 02: Instalación de una Instancia de Oracle Database

## OBJETIVOS
  * Comprender el funcionamiento de un motor de base de datos relacional a través de su instalaciónn y configuración.

## REQUERIMIENTOS
  * Conocimientos: 
    - Conocimientos básicos de administración de base de datos Microsoft SQL Server.
    - Conocimientos básicos de SQL.
  * Hardware:
    - Virtualization activada en el BIOS..
    - CPU SLAT-capable feature.
    - Al menos 4GB de RAM.
  * Software:
    - Windows 10 64bit: Pro, Enterprise o Education (1607 Anniversary Update, Build 14393 o Superior)
    - Docker Desktop 
    - Powershell versión 7.x
    - Cuenta de Oracle (generarla aqui https://container-registry.oracle.com/)
    - SQL Developer (https://www.oracle.com/database/sqldeveloper/technologies/download/)

## CONSIDERACIONES INICIALES
  * Clonar el repositorio mediante git para tener los recursos necesaarios

## DESARROLLO
1. Iniciar la aplicación Docker Desktop:
2. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
3. Ejecutar el siguiente comando para verificar la versión de Docker
```
docker version
```
4. Iniciar sesion en el registro de contenedores de Oracle, colocar su usuario(correo) y contraseña cuando se lo pida. 
```
docker login container-registry.oracle.com
```
5. Descargar la imagen de docker de Microsoft SQL Server
```
docker pull container-registry.oracle.com/database/express:latest
```
6. Verificar la imagen de docker descargada
```
docker images
```
7. Ejecutar e iniciar una instancia de contenedor de la imagen previamente descargada
```
docker run -d -p 16111:1433 -e ‘ACCEPT_EULA=Y’ -e ‘SA_PASSWORD=Upt.2022’ --name SQLLNX01 mcr.microsoft.com/mssql/server
```
8. Verificar la instancia de contenedor este en ejecución
```
docker ps
```
9. Esperar unos 45 segundos y ejecutar el siguiente comando:
```
docker exec -it ORACLE01 sqlplus / as sysdba
```
10. Iniciar una nueva consulta, escribir y ejecutar lo siguiente:
```
SELECT BANNER FROM v$version;
```
11. Para salir escriba el comando exit.

12. En una pestaña nueva del navegador de internet acceder a la siguiente dirección:https://localhost:5500/em e Iniciar sesión con los siguientes datos
```
Usuario: sys
Contraseña: Upt.2022
```
13. Capture la pantalla de su servidor como parte de la actividad del laboratorio, anote el nombre de la instancia (SID).

14. Iniciar el aplicativo Oracle SQL Developer, crear una nueva conexión con los siguientes parámetros:.
```
Name : OracleConexion
Usuario: sys
Contraseña: Upt.2022
Rol: SYSDBA
Nombre del Host: localhost
Puerto: 1521
SID: .......
```
15. Iniciar una nueva consulta, escribir y ejecutar lo siguiente:
```
SELECT * FROM ALL_TABLES
```
15. Iniciar una nueva consulta, escribir (reemplazando con su nombre y Apellido) y ejecutar lo siguiente:
```
CREATE SMALLFILE TABLESPACE 'nombreApellido' DATAFILE 'u01/app/oracle/oradata/orcl/cursos.dbf' SIZE 50M AUTOEXTEND ON NEXT 1M MAXSIZE UNLIMITED LOGGING EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO DEFAULT NOCOMPRESS
```
16. Ejecutar el siguiente comando en Powershell para eliminar el conetenedor generado.
```
docker rm -f SQLLNX01
```
17. Verificar la instancia de contenedor ya no se encuentra activa
```
docker ps
```
---
## Actividades Encargadas
1. Genere un nuevo contenedor y cree un espacio de tablas con las siguientes características.
Nombre : FINANCIERA:
• DATOS (dbf) : Tamaño Inicial : 50MB, Incremento: 10MB, Ilimitado
• INDICES (dbf) Tamaño Inicial : 100MB, Incremento: 20MB, Maximo: 1GB
• HISTORICO (dbf) Tamaño Inicial : 100MB, Incremento: 50MB, Ilimitado
¿Cuál sería el script SQL que generaría esta base de datos?
