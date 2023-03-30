# SESION DE LABORATORIO N° 01: Instalación de una Instancia de Microsoft SQL Server

## OBJETIVOS
  * Comprender el funcionamiento de un motor de base de datos relacional a través de su instalaciónn y configuración.

## REQUERIMIENTOS
  * Conocimientos: 
    - Conocimientos básicos de administración de base de datos.
    - Conocimientos básicos de SQL.
  * Hardware:
    - Virtualization activada en el BIOS..
    - CPU SLAT-capable feature.
    - Al menos 4GB de RAM.
  * Software:
    - Windows 10 64bit: Pro, Enterprise o Education (1607 Anniversary Update, Build 14393 o Superior)
    - Docker Desktop 
    - Powershell versión 7.x
    - DBeaver en su última versión

## CONSIDERACIONES INICIALES
  * Clonar el repositorio mediante git para tener los recursos necesaarios

## DESARROLLO
1. En un navegado de internet, iniciar el laboratorio de Learner Lab en AWS Academy.
2. Una vez iniciado el laboratorio (AWS con semaforo verde), hacer click en la pestaña AWS Details, y hacer click en el boton Show al lado de AWS CLI, copiar los valores
3. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
4. Ejecutar el siguiente comando para guardar las credenciales
```
notepad ./.aws/credentials
```
5. Copiar el contenido del paso 2 en este archivo, una vez copiado, guardar y cerrar el notepad.
6. En el Terminal ejecutar el siguiente comando
```
aws configure
```
7. Presionar 2 veces enter y cuando se solicite Default region name colocar: us-east-1, luego presionar una vez mas enter para terminar la configuración.
6. Verificar la imagen de docker descargada
```
aws rds describe-db-instances
```
7. Ejecutar e iniciar una instancia de contenedor de la imagen previamente descargada
```
aws rds create-db-instance --db-instance-identifier rds-mysql-upt-01 --db-instance-class db.t3.micro --engine mysql --master-username admin --master-user-password upt.2023 --allocated-storage 20
```
8. Verificar la instancia de contenedor este en ejecución
```
docker ps
```
9. Esperar unos segundos e iniciar la aplicación Microsoft SQL Server Management Studio, y conectar con los siguientes datos:
> Servidor: (local),16111  
> Autenticación: SQL Sever  
> Usuario: sa  
> Clave: Upt.2022  

10. Iniciar una nueva consulta, escribir y ejecutar lo siguiente:
```
SELECT @@VERSION
```
11. Cerrar Microsoft SQL Server Management Studio

12. Regresar a Powershell y ejecutar el siguiente commando
```
Install-Module SqlServer
```
13. Ejecutar el siguiente comando el Powershell para consultar la versión del motor de base de datos.
```
Invoke-Sqlcmd -Query 'SELECT @@VERSION' -ServerInstance '(local),16111' -Username 'sa' -Password 'Upt.2022'
```
14. Ejecutar el siguiente comando en Powershell para generar una nueva base de datos.
```
Invoke-Sqlcmd -InputFile lab01-01.sql -ServerInstance '(local),16111' -Username 'sa' -Password 'Upt.2022'
```
15. Ejecutar el siguiente comando en Powershell para verificar que se ha generado la base de datos.
```
Invoke-Sqlcmd -Query 'SELECT * FROM sys.databases WHERE name = ''BIBLIOTECA''' -ServerInstance '(local),16111' -Username 'sa' -Password 'Upt.2022'
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
1. ¿Con qué comando(s) exportaría la imagen de Docker de Microsoft SQL Server a otra PC o servidor?
2. ¿Con qué comando(s) podría generar dos volúmenes para un contenedor para distribuir en un volumen el Archivo
de Datos (.mdf) y en otro el Archivo Log (.ldf)?
3. Genere un nuevo contenedor y cree la base de datos con las siguientes características.
   Nombre : FINANCIERA
   Archivos:
   • DATOS (mdf) : Tamaño Inicial : 50MB, Incremento: 10MB, Ilimitado
   • INDICES (ndf) Tamaño Inicial : 100MB, Incremento: 20MB, Maximo: 1GB
   • HISTORICO (ndf) Tamaño Inicial : 100MB, Incremento: 50MB, Ilimitado
   • LOG (ldf) Tamaño Inicial : 10MB, Incremento: 10MB, Ilimitado
   ¿Cuál sería el script SQL que generaría esta base de datos?
