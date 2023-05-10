# SESION DE LABORATORIO N° 01: Creación de un API utilizando una base de datos Clave-Valor

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
    - DBeaver Community Edition

## CONSIDERACIONES INICIALES
  * Clonar el repositorio mediante git para tener los recursos necesaarios

## DESARROLLO

### PARTE I: Desplegando la base de datos

1. Iniciar la aplicación Docker Desktop:
2. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
3. Ejecutar el siguiente comando para crear una nueva instancia de la base de datos Redis
```
docker run --name redisdb -p 6379:6379 -d redis
```
4. Ejecutar el siguiente comando para iniciar sesión y conectarse al gestor de base de datos
```
docker exec -it redisdb redis-cli
```
5. Una vez dentro de interfaz de linea de comandos, ejecutar los siguientes comandos para establecer un objeto clave-valor y luego recuperarlo
```
set clave "upt2023"
get clave
```
6. Finalmente escribir "exit" para salir de interfaz de linea de comandos de Redis

### PARTE II: Construyendo el API

1. Iniciar la aplicación Powershell o Windows Terminal en modo administrador si es que no se tiene iniciada y ejecutar el siguiente comando
```
dotnet new webapi -o App.Redis.Api
```
2. Acceder a la carpeta recien creada y adicionar la libreria de Redis para Net.
```
cd .\App.Redis.Api\
dotnet add pakcage Microsoft.Extensions.Caching.StackExchangeRedis
```
3. Iniciar Visual Studio Code tomando como base la carpeta generada (App.Redis.Api). Dentro de la carpeta Controllers generar un archivo TodosController.cs e introducir el siguiente código
```C#
using Microsoft.AspNetCore.Mvc;

namespace App.Redis.Api.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class TodosController : ControllerBase
    {
	List<string> todos = new List<string> { "shopping", "Watch Movie", "Gardening" };

        [HttpGet(Name = "All")]
        public async Task<IActionResult> GetAll()
        {
            List<string> myTodos = todos;
            bool IsCached = false;
            return Ok(new { IsCached, myTodos });
        }
    }
}
```
4. Abrir un terminal en Visual Studio Code (Ctrl+Ñ) o voler al terminal abierto, y ejecutar los siguientes comandos para compitar y ejecutar la aplicación. 
```
dotnet build
dotnet run
```
5. Despues de unos segundos se pintara en el terminal el acceso la url del API REST creado, por ejemplo http://localhost:5162/Todos (tener en cuenta que el puerto puede variear en su PC). Abrir un navegador de internet y pedar esa url. El resultado beria ser similar a:
```JSON
{"isCached":false,"myTodos":["shopping","Watch Movie","Gardening"]}
```
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
