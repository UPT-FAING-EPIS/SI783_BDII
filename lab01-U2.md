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
5. Una vez dentro de interfaz de linea de comandos (CLI), ejecutar los siguientes comandos para establecer un objeto clave-valor y luego recuperarlo
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
dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis
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
5. Despues de unos segundos se pintara en el terminal el acceso la url del API REST creado, por ejemplo http://localhost:5162/Todos (tener en cuenta que el puerto puede variear en su PC). Abrir un navegador de internet y colocar la URL anterior. El resultado a obtener deberia ser similar a:
```JSON
{"isCached":false,"myTodos":["shopping","Watch Movie","Gardening"]}
```
6. Volver a Visual Studio Code y en el archivo Program.cs agregar la siguiente linea después de `var builder = WebApplication.CreateBuilder(args)`:
```C#
builder.Services.AddStackExchangeRedisCache( options => options.Configuration = "localhost:6379" );
```
7. Modificar el archivo TodosController.cs con el siguiente código:
```C#
using System.Text.Json;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Caching.Distributed;

namespace App.Redis.Api.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class TodosController : ControllerBase
    {
	    List<string> todos = new List<string> { "shopping", "Watch Movie", "Gardening" };
        private readonly IDistributedCache _distributedCache;

        public TodosController(IDistributedCache distributedCache)
        {
            _distributedCache = distributedCache;
        }

        [HttpGet(Name = "All")]
        public async Task<IActionResult> GetAll()
        {
            List<string> myTodos = new List<string>();
            bool IsCached = false;
            string? cachedTodosString = string.Empty;
            cachedTodosString = await _distributedCache.GetStringAsync("_todos");
            if (!string.IsNullOrEmpty(cachedTodosString))
            {
                // loaded data from the redis cache.
                myTodos = JsonSerializer.Deserialize<List<string>>(cachedTodosString);
                IsCached = true;
            }
            else
            {
                // loading from code (in real-time from database)
                // then saving to the redis cache 
                myTodos = todos;
                IsCached = false;
                cachedTodosString = JsonSerializer.Serialize<List<string>>(todos);
                await _distributedCache.SetStringAsync("_todos", cachedTodosString);
            }
            return Ok(new { IsCached, myTodos });
        }
    }
}
```
8. Ejecutar el paso 4 (Parte II) para ejecutar la aplicación y luego verificar como indica el paso 5 (Parte II). Se deberia obtener dos resultados
```JSON
// Primer request
{"isCached":false,"myTodos":["shopping","Watch Movie","Gardening"]}
// Posteriores requests
{"isCached":true,"myTodos":["shopping","Watch Movie","Gardening"]}
```
9. Ejecutar el paso 4 (Parte I) para iniciar la CLI de Redis y escribir el siguiente comando.
```
keys *
```
10. Despues de esto la CLI deberia responder con el siguiente resultado.
```
1) "_todos"
```



### Parte III: Estableciendo Expiración al cache de Datos

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
1. Genere un nuevo controlador que se conecte a una tabla de una base de datos relacional y guarde la información en Redis y luego pueda consultarla desde Redis con una expiraciòn de 10 minutos.
