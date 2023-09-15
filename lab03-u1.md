# SESION DE LABORATORIO N° 03: Instalación de una instancia de base de datos en AWS

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
    - AWS CLI
    - DBeaver en su última versión

## CONSIDERACIONES INICIALES
  * Clonar el repositorio mediante git para tener los recursos necesaarios
aws ec2 authorize-security-group-ingress --group-id sg-0c9ec60f3f780e48c --protocol tcp --port 3306 --cidr 0.0.0.0/0
https://www.linuxfoundation.org/blog/blog/an-introduction-to-the-aws-command-line-tool-part-2
## DESARROLLO

### PARTE I: CREACION DE LA BASE DE DATOS
1. En el navegador de internet, iniciar el laboratorio de Learner Lab en AWS Academy.
2. Una vez iniciado el laboratorio (AWS con semaforo verde), hacer click en la pestaña AWS Details, y hacer click en el boton Show al lado de AWS CLI, copiar los valores

![image](https://github.com/UPT-FAING-EPIS/SI783_BDII/assets/10199939/f31c02f0-76ed-468b-9733-2cee0edd44e0)

3. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
4. En el Terminal, ejecutar el siguiente comando para guardar las credenciales en la ruta ~/.aws/credentials.
```
cd
mkdir .aws
notepad ./.aws/credentials
```
> Copiar el contenido del paso 2 en este archivo, una vez copiado, guardar y cerrar el notepad.
5. En el Terminal, ejecutar el siguiente comando para guardar las credenciales en la ruta ~/.aws/config.
```
notepad ./.aws/credentials
```
> colocar el siguiente contenido
```
[default]
region = us-east-1
```
> Una vez copiado, guardar y cerrar el notepad.
6. En el Terminal, ejecutar el siguiente comando para crear una nueva base de datos.

8. Verificar las instancias de base de datos creadas.
```
aws rds describe-db-instances
```
9. Crear una instancia de base de datos
```
aws rds create-db-instance --db-instance-identifier rds-upt-mysql-01 --db-instance-class db.t3.micro --engine mysql --master-username admin --master-user-password upt.2023 --allocated-storage 20 --backup-retention-period 0
```
10. Verificar la instancia de base de datos haya sido creada
```
aws rds describe-db-instances
```
11. Esperar unos segundos e iniciar la aplicación DBeaver, y conectar con los siguientes datos:
> Servidor: (url o nombre publico)  
> Puerto: 3306  
> Usuario: admin  
> Clave: upt.2023

12. Iniciar una nueva consulta, escribir y ejecutar lo siguiente:
```
SELECT VERSION();
```
13. Cerrar DBeaver

14. Iniciar la consola de AWS del modulo Learner Lab y verificar la creación y ejecuciòn de la instancia de base de datos (Realizar una captura de pantalla para su informe)

15. Ejecutar el siguiente comando en Powershell para eliminar la instancia generado.
```
aws rds delete-db-instance --db-instance-identifier rds-mysql-upt-01 --skip-final-snapshot
```
16. Verificar la instancia de contenedor ya no se encuentra activa
```
aws rds describe-db-instances
```
---
## Actividades Encargadas
1. Generar uns nueva instancia de base de datos utilizando Terraform.


[comment]: <> (aws rds create-db-instance --engine sqlserver-ex --engine-version 14.00.3281.6.v1 --db-instance-identifier rds-upt-mssql-01 --allocated-storage 20 --db-instance-class db.t3.small --master-username admin --master-user-password upt.2023 --backup-retention-period 0 --storage-type standard --port 1433 --publicly-accessible)
[comment]: <> (https://www.mssqltips.com/sqlservertip/7469/aws-cli-deploy-amazon-rds-sql-server-instance/)
[comment]: <> (https://www.tutorialspoint.com/amazonrds/amazonrds_postgressql_creating_db.htm)
