# SESION DE LABORATORIO N° 02: Generando un API utilizando una Base de Datos Documental (MongoDB)

## OBJETIVOS
  * Comprender el funcionamiento de un motor de base de datos no relacional del tipo documental a través de su despliegue y uso.

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
    - .Net 6

## CONSIDERACIONES INICIALES
  * Clonar el repositorio mediante git para tener los recursos necesaarios

## DESARROLLO

### PARTE I: Configuración de la base de datos

1. Iniciar la aplicación Docker Desktop:
2. Iniciar la aplicación Powershell o Windows Terminal en modo administrador 
3. Ejecutar el siguiente comando para iniciar una nueva instancia de una base de datos Mongo
```
docker run --name mongodb -d -p 27017:27017 mongo
```
4. Verificar sus ejecución y nombre con el siguiente comando. 
```
docker ps
```
deberá visualizar algo similar a:
```
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                      NAMES
f293cca76b24   mongo     "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:27017->27017/tcp   mongodb
```
5. Conectarse al gestor de base de datos mediante el siguiente comando:
```
docker exec -it mongodb mongosh
```
6. Crear la base de datos BookstoreDB, con el siguiente comando:
```
use BookstoreDb
```
7. Crear la colección Books, dentro de la base de datos:
```
db.createCollection('Books')
```
8. Insertar algunos datos de pruebas en la colección Books:
```
db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
```
9. Verificar los datos ingresados mediante el siguiente comando:
```
db.Books.find({}).pretty()
```

### PARTE II: Creación del API

1. Iniciar la aplicación Powershell o Windows Terminal en modo administrador si es que no se tiene iniciada y ejecutar el siguiente comando:
```
dotnet new webapi -o BooksApi
```
2. Acceder a la carpeta recien creada y adicionar la libreria de Mongo para Net.
```
cd ./BooksApi/
dotnet add package MongoDB.Driver
```
3. Iniciar Visual Studio Code tomando como base la carpeta generada (BooksApi). Dentro del proyecto añadir el archivo Book.cs el cual tendrña la clase entidad, con el siguiente código
```C#
using MongoDB.Bson;
using MongoDB.Bson.Serialization.Attributes;

namespace BooksApi.Models
{
    public class Book
    {
        [BsonId]
        [BsonRepresentation(BsonType.ObjectId)]
        public string Id { get; set; }
        [BsonElement("Name")]
        public string BookName { get; set; }
        public decimal Price { get; set; }
        public string Category { get; set; }
        public string Author { get; set; }
    }
}
```
5. Ahora proceder a añadir la clase de configuración con el nombre de archivo BookstoreDatabaseSettings.cs e introducir el siguiente código:
```C#
namespace BooksApi.Models
{
    public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
    {
        public string BooksCollectionName { get; set; }
        public string ConnectionString { get; set; }
        public string DatabaseName { get; set; }
    }

    public interface IBookstoreDatabaseSettings
    {
        string BooksCollectionName { get; set; }
        string ConnectionString { get; set; }
        string DatabaseName { get; set; }
    }
}
```
4. Modificar el archivo appsettings.json para que apunte als BD de datos y añadir:
```JSON
{
  "BookstoreDatabaseSettings": {
    "BooksCollectionName": "Books",
    "ConnectionString": "mongodb://localhost:27017",
    "DatabaseName": "BookstoreDb"
  },
  "Logging": {
    "IncludeScopes": false,
    "Debug": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "Console": {
      "LogLevel": {
        "Default": "Warning"
      }
    }
  }
}
```
5. Crear el archivo BookService.cs con el siguiente código:
```C#
using BooksApi.Models;
using MongoDB.Driver;
using System.Collections.Generic;
using System.Linq;

namespace BooksApi.Services
{
    public class BookService
    {
        private readonly IMongoCollection<Book> _books;

        public BookService(IBookstoreDatabaseSettings settings)
        {
            var client = new MongoClient(settings.ConnectionString);
            var database = client.GetDatabase(settings.DatabaseName);

            _books = database.GetCollection<Book>(settings.BooksCollectionName);
        }

        public List<Book> Get() =>
            _books.Find(book => true).ToList();

        public Book Get(string id) =>
            _books.Find<Book>(book => book.Id == id).FirstOrDefault();

        public Book Create(Book book)
        {
            _books.InsertOne(book);
            return book;
        }

        public void Update(string id, Book bookIn) =>
            _books.ReplaceOne(book => book.Id == id, bookIn);

        public void Remove(Book bookIn) =>
            _books.DeleteOne(book => book.Id == bookIn.Id);

        public void Remove(string id) => 
            _books.DeleteOne(book => book.Id == id);
    }
}
```
6. En el archivo Program.cs adicionar el siguiente código.
```C#
    services.Configure<BookstoreDatabaseSettings>(
        Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

    services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
        sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

    services.AddSingleton<BookService>();
```
7. Adicionalmente crear el archivo BooksController.cs en la carpeta Controllers con el siguiente código:
```C#
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);
            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);
            if (book == null) return NotFound();
            _bookService.Update(id, bookIn);
            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);
            if (book == null) return NotFound();
            _bookService.Remove(id);
            return NoContent();
        }
    }
}
```
8. Iniciar un terminal en VS Code (CTRL+Ñ) o volver al terminal anteriomente abierto y ejecutar la aplicación con el comando:
```
dotnet run
```
9. Iniciar un navegador de internet e introducir la url http://localhost:XXXXX/swagger (donde XXXXX es el puerto donde esta eejcutandose la aplicación), una vez cargada la interfaz de Swagger, en el metodo Create, ingresar los siguientes datos y probar:
```JSON
{
  "id":"{ID}",
  "bookName":"Clean Code",
  "price":43.15,
  "category":"Computers",
  "author":"Robert C. Martin"
}
```

---
## Actividades Encargadas
1. Genere una base de datos en Mongo Atlas (https://www.mongodb.com/atlas/database) y modifique la cadena de conexión de la aplicaciòn para utilizar esta base de datos.

