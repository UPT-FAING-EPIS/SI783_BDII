SESION DE LABORATORIO N° 01
Instalación de una Instancia de Microsoft SQL Server

1. OBJETIVOS
* Comprender el funcionamiento de un motor de base de datos relacional a través de su instalaciónn y configuración.
2. REQUERIMIENTOS
* Conocimientos: Para el desarrollo de esta práctica se requerirá de los siguientes conocimientos básicos:
- Conocimientos básicos de administración de base de datos Microsoft SQL Server.
- Conocimientos básicos de SQL.
* Hardware
- Virtualization activada en el BIOS..
- CPU SLAT-capable feature.
- Al menos 4GB de RAM.
* Software
Asimismo se necesita los siguientes aplicativos:
- Windows 10 64bit: Pro, Enterprise o Education (1607 Anniversary Update, Build 14393 o Superior)
- Docker Desktop 
- Microsoft SQL Server Management Studio en su última versión

3. CONSIDERACIONES INICIALES
* Crear dos carpetas en una unidad donde se pueda modificar datos DATALNX y DATAWIN

4. DESARROLLO
4.1. Iniciar la aplicación Docker Desktop:
4.2. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
4.3. Ejecutar el siguiente comando para verificar la versión de Docker
```
docker version
```
4.4. Ejecutar el siguiente comando para buscar las imagenes de conetenedores mssql
```
docker search mssql
```
4.5. Descargar la imagen de docker de Microsoft SQL Server
```
docker pull mcr.microsoft.com/mssql/server
```
4.6. Verificar la imagen descargada
```
docker images
```
4.7. Ejecutar e iniciar una instancia de contenedor de la imagen previamente descargada
```
docker run -d -p 16111:1433 -e ‘ACCEPT_EULA=Y’ -e ‘SA_PASSWORD=Upt.2022’ --name SQLLNX01 mcr.microsoft.com/mssql/server
```
