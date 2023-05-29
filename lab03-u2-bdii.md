# SESION DE LABORATORIO N° 03: Generando un API utilizando una Base de Datos de Grafos (Neo4j)

## OBJETIVOS
  * Comprender el funcionamiento de un motor de base de datos no relacional del tipo grafos a través de su despliegue y uso.

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
3. Ejecutar el siguiente comando para iniciar una nueva instancia de una base de datos Neo4j
```
docker run --name neo4j-db -d -p 7474:7474 -p 7687:7687 -v neo4jvol:/data -e NEO4J_AUTH=neo4j/upt.2023 neo4j
```
4. Verificar sus ejecución y nombre con el siguiente comando. 
```
docker ps
```
deberá visualizar algo similar a:
```
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                             PORTS                                 NAMES
00055de0745c   neo4j  "tini -g -- /startup…"   33 seconds ago    Up 32 seconds   0.0.0.0:7474->7474/tcp, 7473/tcp, 0.0.0.0:7687->7687/tcp   neo4j-db
```
5. Abrir un navegador de internet e ingresar la url http://localhost:7474/, ingresar lasa credenciales usuario y clave neo4j:neo4j y cambiar la clave del mismo que servira como cadena de conexión para la realizacion del laboratorio.

7. Conectarse al gestor de base de datos mediante el siguiente comando:
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
dotnet new webapi -o App.Neo4j.Api
```
2. Acceder a la carpeta recien creada y adicionar la libreria de Mongo para Net.
```
cd ./App.Neo4j.Api/
dotnet add package Neo4j.Driver
```
3. Iniciar Visual Studio Code tomando como base la carpeta generada (App.Neo4j.Api). Dentro del proyecto crear una nueva carpeta llamada Models y dentro de estas adicionar los siguientes archivos:
> Person.cs
```C#
namespace App.Neo4j.Api.Models;
public record Person(string Name, string Job, string Role);
```
> Movie.cs
```C#
using System.Collections.Generic;
namespace App.Neo4j.Api.Models;
public record Movie(string Title, IEnumerable<Person> Cast = null, long? Released = null, string Tagline = null,
    long? Votes = null);
```
> D3Graph.cs
```C#
using System.Collections.Generic;
namespace App.Neo4j.Api.Models;
public record D3Graph(IEnumerable<D3Node> Nodes, IEnumerable<D3Link> Links);
public record D3Node(string Title, string Label);
public record D3Link(int Source, int Target);
```
4. Ahora proceder a añadir la clase de para el acceso a dtos en este caso a atrves de un repositorio, crear la carpeta Repositories en la raiz del proyecto y añadir un nuevo archivo con el nombre de archivo MovieRepository.cs e introducir el siguiente código:
```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using App.Neo4j.Api.Models;
using Neo4j.Driver;

namespace App.Neo4j.Api.Repositories;

public interface IMovieRepository
{
    Task<Movie> FindByTitle(string title);
    Task<int> VoteByTitle(string title);
    Task<List<Movie>> Search(string search);
    Task<D3Graph> FetchD3Graph(int limit);
}

public class MovieRepository : IMovieRepository
{
    private readonly IDriver _driver;

    public MovieRepository(IDriver driver)
    {
        _driver = driver;
    }

    public async Task<Movie> FindByTitle(string title)
    {
        if (title == "favicon.ico")
            return null;
        
        await using var session = _driver.AsyncSession(WithDatabase);

        return await session.ExecuteReadAsync(async transaction =>
        {
            var cursor = await transaction.RunAsync(@"
                        MATCH (movie:Movie {title:$title})
                        OPTIONAL MATCH (movie)<-[r]-(person:Person)
                        RETURN movie.title AS title,
                               collect({
                                   name:person.name,
                                   job: head(split(toLower(type(r)),'_')),
                                   role: reduce(acc = '', role IN r.roles | acc + CASE WHEN acc='' THEN '' ELSE ', ' END + role)}
                               ) AS cast",
                new {title}
            );
            
            return await cursor.SingleAsync(record => new Movie(
                record["title"].As<string>(),
                MapCast(record["cast"].As<List<IDictionary<string, object>>>())
            ));
        });
    }

    public async Task<int> VoteByTitle(string title)
    {
        await using var session = _driver.AsyncSession(WithDatabase);
        return await session.ExecuteWriteAsync(async transaction =>
        {
            var cursor = await transaction.RunAsync(@"
                            MATCH (m:Movie {title: $title})
                            SET m.votes = coalesce(m.votes, 0) + 1;",
                new {title}
            );

            var summary = await cursor.ConsumeAsync();
            return summary.Counters.PropertiesSet;
        });
    }

    public async Task<List<Movie>> Search(string search)
    {
        await using var session = _driver.AsyncSession(WithDatabase);
        return await session.ExecuteReadAsync(async transaction =>
        {
            var cursor = await transaction.RunAsync(@"
                        MATCH (movie:Movie)
                        WHERE toLower(movie.title) CONTAINS toLower($title)
                        RETURN movie.title AS title,
                               movie.released AS released,
                               movie.tagline AS tagline,
                               movie.votes AS votes",
                new {title = search}
            );

            return await cursor.ToListAsync(record => new Movie(
                record["title"].As<string>(),
                Tagline: record["tagline"].As<string>(),
                Released: record["released"].As<long>(),
                Votes: record["votes"]?.As<long>()
            ));
        });
    }

    public async Task<D3Graph> FetchD3Graph(int limit)
    {
        await using var session = _driver.AsyncSession(WithDatabase);
        return await session.ExecuteReadAsync(async transaction =>
        {
            var cursor = await transaction.RunAsync(@"
                        MATCH (m:Movie)<-[:ACTED_IN]-(p:Person)
                        WITH m, p
                        ORDER BY m.title, p.name
                        RETURN m.title AS title, collect(p.name) AS cast
                        LIMIT $limit",
                new {limit}
            );

            var nodes = new List<D3Node>();
            var links = new List<D3Link>();

            // IAsyncEnumerable available from Version 5.5 of .NET Driver.
            await foreach (var record in cursor)
            {
                var movie = new D3Node(record["title"].As<string>(), "movie");
                var movieIndex = nodes.Count;
                nodes.Add(movie);
                foreach (var actorName in record["cast"].As<IList<string>>())
                {
                    var actor = new D3Node(actorName, "actor");
                    var actorIndex = nodes.IndexOf(actor);
                    actorIndex = actorIndex == -1 ? nodes.Count : actorIndex;
                    nodes.Add(actor);
                    links.Add(new D3Link(actorIndex, movieIndex));
                }
            }

            return new D3Graph(nodes, links);
        });
    }

    private static IEnumerable<Person> MapCast(IEnumerable<IDictionary<string, object>> persons)
    {
        return persons
            .Select(dictionary =>
                new Person(
                    dictionary["name"].As<string>(),
                    dictionary["job"].As<string>(),
                    dictionary["role"].As<string>()
                )
            ).ToList();
    }

    private static void WithDatabase(SessionConfigBuilder sessionConfigBuilder)
    {
        var neo4jVersion = Environment.GetEnvironmentVariable("NEO4J_VERSION") ?? "";
        if (!neo4jVersion.StartsWith("4"))
            return;

        sessionConfigBuilder.WithDatabase(Database());
    }

    private static string Database()
    {
        return Environment.GetEnvironmentVariable("NEO4J_DATABASE") ?? "movies";
    }
}
```
6. En el archivo Program.cs adicionar el siguiente código.
```C#
//Al inicio
using System;
using App.Neo4j.Api.Repositories;
using Neo4j.Driver;
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
...
// Add services to the container.
builder.Services.AddScoped<IMovieRepository, MovieRepository>();
builder.Services.AddSingleton(GraphDatabase.Driver(
    Environment.GetEnvironmentVariable("NEO4J_URI") ?? "neo4j+s://demo.neo4jlabs.com",
    AuthTokens.Basic(
        Environment.GetEnvironmentVariable("NEO4J_USER") ?? "movies",
        Environment.GetEnvironmentVariable("NEO4J_PASSWORD") ?? "movies"
    )
));
...
// Antes de app.UseHttpsRedirection();
app.UseDefaultFiles();
app.UseStaticFiles();
```
7. Adicionalmente crear el archivo MoviesController.cs en la carpeta Controllers con el siguiente código:
```C#
using System.Threading.Tasks;
using System.Collections.Generic;
using App.Neo4j.Api.Models;
using App.Neo4j.Api.Repositories;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

namespace App.Neo4j.Api.Controllers;

[ApiController]
[Route("movie")]
public class MoviesController : Controller
{
    private readonly IMovieRepository _movieRepository;

    public MoviesController(IMovieRepository movieRepository)
    {
        _movieRepository = movieRepository;
    }

    [Route("{title}")]
    [HttpGet]
    public Task<Movie> GetMovieDetails([FromRoute] string title)
    {
        return _movieRepository.FindByTitle(title);
    }

    [Route("{title}/vote")]
    [HttpPost]
    public Task<int> VoteInMovie([FromRoute] string title)
    {
        return _movieRepository.VoteByTitle(title);
    }
    [Route("search")]
    [HttpGet]
    public Task<List<Movie>> SearchMovies([FromQuery(Name = "q")] string search)
    {
        return _movieRepository.Search(search);
    }
    [Route("graph")]
    [HttpGet]
    public Task<D3Graph> FetchD3Graph([FromQuery] int limit)
    {
        return _movieRepository.FetchD3Graph(limit <= 0 ? 50 : limit);
    }  
}
```
8. Finalmente crear una carpeta wwwroot en donde se debe adicioanr un archivo index.html con el siguiente contenido:
```Html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://neo4j-documentation.github.io/developer-resources/language-guides/assets/css/main.css">
    <title>Neo4j Movies</title>
</head>

<body>
    <div id="graph">
    </div>
    <div role="navigation" class="navbar navbar-default navbar-static-top">
        <div class="container">
            <div class="row">
                <div class="col-sm-6 col-md-6">
                    <ul class="nav navbar-nav">
                        <li>
                            <form role="search" class="navbar-form" id="search">
                                <div class="form-group">
                                    <input type="text" value="Matrix" placeholder="Search for Movie Title" class="form-control" name="search">
                                </div>
                                <button class="btn btn-default" type="submit">Search</button>
                            </form>
                        </li>
                    </ul>
                </div>
                <div class="navbar-header col-sm-6 col-md-6">
                    <div class="logo-well">
                        <a href="https://neo4j.com/developer/get-started">
                            <img src="https://neo4j-documentation.github.io/developer-resources/language-guides/assets/img/logo-white.svg" alt="Neo4j World's Leading Graph Database" id="logo">
                        </a>
                    </div>
                    <div class="navbar-brand">
                        <div class="brand">Neo4j Movies</div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="row">
        <div class="col-md-5">
            <div class="panel panel-default">
                <div class="panel-heading">Search Results</div>
                <table id="results" class="table table-striped table-hover">
                    <thead>
                        <tr>
                            <th>Movie</th>
                            <th>Released</th>
                            <th>Tagline</th>
                            <th>Votes</th>
                        </tr>
                    </thead>
                    <tbody>
                    </tbody>
                </table>
            </div>
        </div>
        <div class="col-md-7">
            <div class="panel panel-default">
                <div class="panel-heading" id="title">Details</div>
                <div class="row">
                    <div class="col-sm-4 col-md-4">
                        <img src="" class="well" id="poster" />
                    </div>
                    <div class="col-md-8 col-sm-8">
                        <h4>Crew</h4>
                        <ul id="crew">
                        </ul>
                    </div>
                </div>
                <div class="panel-footer"><button id="vote">Vote</button></div>
            </div>
        </div>
    </div>
    <style type="text/css">
        .node {
            stroke: #222;
            stroke-width: 1.5px;
        }

            .node.actor {
                fill: #888;
            }

            .node.movie {
                fill: #BBB;
            }

        .link {
            stroke: #999;
            stroke-opacity: .6;
            stroke-width: 1px;
        }
    </style>

    <script type="text/javascript" src="//code.jquery.com/jquery-1.11.0.min.js"></script>
    <script src="https://d3js.org/d3.v3.min.js" type="text/javascript"></script>
    <script type="text/javascript">$(function () {
        function showMovie(title) {
            $.get("/movie/" + encodeURIComponent(title),
                    function (data) {
                        if (!data) return;
                        $("#title").text(data.title);
                        $("#poster").attr("src","https://neo4j-documentation.github.io/developer-resources/language-guides/assets/posters/"+encodeURIComponent(data.title)+".jpg");
                        const $list = $("#crew").empty();
                        data.cast.forEach(function (cast) {
                            $list.append($("<li>" + cast.name + " " +cast.job + (cast.job == "acted"?" as " + cast.role : "") + "</li>"));
                        });
                        $("#vote")
                            .unbind("click")
                            .click(function () {
                                voteInMovie(data.title)
                            });
                    }, "json");
            return false;
        }
        function search(showFirst = true) {
            const query=$("#search").find("input[name=search]").val();
            $.get("/movie/search?q=" + encodeURIComponent(query),
                    function (data) {
                        const t = $("table#results tbody").empty();
                        if (!data || data.length == 0) return;
                        data.forEach(function (movie, index) {
                            $("<tr><td class='movie'>" + movie.title
                                + "</td><td>" + movie.released
                                + "</td><td>" + movie.tagline
                                + "</td><td id='votes" + index + "'>" + (movie.votes || 0)
                                + "</td></tr>").appendTo(t)
                                    .click(function() { showMovie($(this).find("td.movie").text());})
                        });
                        
                        if (showFirst) {
                            showMovie(data[0].title);
                        }
                    }, "json");
            return false;
        }

        function voteInMovie(title) {
            $.post("/movie/" + encodeURIComponent(title) + "/vote", () => {
                search(false);
                showMovie(title);
            });
        }

        $("#search").submit(search);
        search();
    })</script>

    <script type="text/javascript">const width = 800, height = 800;

    const force = d3.layout.force()
            .charge(-200).linkDistance(30).size([width, height]);

    const svg = d3.select("#graph").append("svg")
            .attr("width", "100%").attr("height", "100%")
            .attr("pointer-events", "all");

    d3.json("/movie/graph", function(error, graph) {
		if (error) return;

        force.nodes(graph.nodes).links(graph.links).start();

        const link = svg.selectAll(".link")
                .data(graph.links).enter()
                .append("line").attr("class", "link");

        const node = svg.selectAll(".node")
                .data(graph.nodes).enter()
                .append("circle")
                .attr("class", function (d) { return "node "+d.label })
                .attr("r", 10)
                .call(force.drag);

        // html title attribute
        node.append("title")
                .text(function (d) { return d.title; })

        // force feed algo ticks
        force.on("tick", function() {
            link.attr("x1", function(d) { return d.source.x; })
                    .attr("y1", function(d) { return d.source.y; })
                    .attr("x2", function(d) { return d.target.x; })
                    .attr("y2", function(d) { return d.target.y; });

            node.attr("cx", function(d) { return d.x; })
                    .attr("cy", function(d) { return d.y; });
        });
    });</script>
</body>
</html>
```
9. Iniciar un terminal en VS Code (CTRL+Ñ) o volver al terminal anteriomente abierto y ejecutar la aplicación con el comando:
```Bash
dotnet run
```
10. Iniciar un navegador de internet e introducir la url http://localhost:XXXXX (donde XXXXX es el puerto donde esta eejcutandose la aplicación), una vez cargada la interfaz ingresar la usqueda de una pelicula "Matrix" por ejemplo.
![image](https://github.com/UPT-FAING-EPIS/SI783_BDII/assets/10199939/d9f25bde-2c27-404a-a43e-bfcdaea434b1)

---
## Actividades Encargadas
1. Genere una base de datos en Neo4j y relice un nuevo proyecto API para su consulta.
