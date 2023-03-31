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

## DESARROLLO
1. En un navegado de internet, iniciar el laboratorio de Learner Lab en AWS Academy.
2. Una vez iniciado el laboratorio (AWS con semaforo verde), hacer click en la pestaña AWS Details, y hacer click en el boton Show al lado de AWS CLI, copiar los valores
3. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
4. Ejecutar el siguiente comando para guardar las credenciales (de ser necesario crear previamente la carpeta .aws)
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
8. Ejecutar e iniciar una instancia de contenedor de la imagen previamente descargada
```
aws rds create-db-instance --db-instance-identifier rds-mysql-upt-01 --db-instance-class db.t3.micro --engine mysql --master-username admin --master-user-password upt.2023 --allocated-storage 20 --backup-retention-period 0
```
9. Verificar la instancia de base de datos haya sido creada
```
aws rds describe-db-instances
```
10. Esperar unos segundos e iniciar la aplicación DBeaver, y conectar con los siguientes datos:
> Servidor: (url o nombre publico)  
> Puerto: 3306  
> Usuario: admin  
> Clave: upt.2023

11. Iniciar una nueva consulta, escribir y ejecutar lo siguiente:
```
SELECT VERSION();
```
12. Cerrar DBeaver

13. Iniciar la consola de AWS del modulo Learner Lab y verificar la creación y ejecuciòn de la instancia de base de datos (Realizar una captura de pantalla para su informe)

14. Ejecutar el siguiente comando en Powershell para eliminar la instancia generado.
```
aws rds delete-db-instance --db-instance-identifier rds-mysql-upt-01 --skip-final-snapshot
```
15. Verificar la instancia de contenedor ya no se encuentra activa
```
aws rds describe-db-instances
```
---
## Actividades Encargadas
1. Generar uns nueva instancia de base de datos utilizando Terraform.
