# API CRUD de Contatos

<fig>
<img src="https://img.freepik.com/fotos-gratis/conceito-de-rpa-com-tela-de-toque-de-mao-embacada_23-2149311914.jpg?w=740&t=st=1709862176~exp=1709862776~hmac=7d67d83128ba9fefe8083046658db0cd22dbf7da3c9196850f231392b38afe50" alt="Uma imagem relacionada ao projeto">
<figcaption>
</fig>

## Inicialização
Para executar o projeto, utilize as ferramentas descritas na sessão *Ferramentas*.

## Ferramentas
* [VS Code](https://code.visualstudio.com/) - Editor de código-fonte leve e gratuito.
* [.NET SDK](https://dotnet.microsoft.com/download) - SDK do .NET para desenvolvimento em C#.
* [SQL Server Express](https://www.microsoft.com/pt-br/sql-server/sql-server-downloads) - Versão gratuita do SQL Server.

## Links importantes
* [ASP.NET Core Documentation](https://docs.microsoft.com/pt-br/aspnet/core/?view=aspnetcore-6.0) - Documentação oficial do ASP.NET Core.

# API CRUD de Contatos

## Introdução

Este projeto consiste em desenvolver uma API para realizar operações CRUD (Create, Read, Update, Delete) em contatos. O objetivo principal do projeto é facilitar a gestão de contatos, permitindo a inserção, consulta, atualização e exclusão de informações de contato.

## Análise técnica

### Descrição do ambiente técnico

O sistema é uma API RESTful desenvolvida em C#, utilizando o framework ASP.NET Core. O banco de dados utilizado é o SQL Server para armazenar os dados dos contatos.

### Levantamento de requisitos  
Os requisitos foram validados com o cliente e aprovados.

### Requisitos Funcionais
O sistema deve atender aos seguintes requisitos:

* **RF1** - Permitir a criação de um novo contato.
* **RF2** - Permitir a consulta de um contato pelo seu identificador.
* **RF3** - Permitir a consulta de contatos por nome.
* **RF4** - Permitir a atualização dos dados de um contato.
* **RF5** - Permitir a exclusão de um contato.

## Regras de Negócio

_Solicitação_  

**RGN1** -  O cliente só poderá realizar operações se estiver autenticado no sistema.

## Casos de Uso

**UC1** - *Criar um novo contato*

Permite a inserção de um novo contato na base de dados.

**UC2** - *Consultar um contato pelo ID*

Permite a consulta de um contato específico utilizando o seu identificador único.

**UC3** - *Consultar contatos por nome*

Permite a consulta de contatos utilizando parte ou todo o nome do contato como filtro.

**UC4** - *Atualizar um contato*

Permite a atualização dos dados de um contato existente na base de dados.

**UC5** - *Excluir um contato*

Permite a exclusão de um contato da base de dados.

### Mensagens internas

Rotas utilizadas pela API para executar métodos HTTP (POST, GET, PUT, DELETE) no banco de dados.

| Nome | Funcionalidade|
|------|--------------|
|```POST``` /Contato|Cria um novo contato.|
|```GET``` /Contato/{id}|Consulta um contato pelo ID.|
|```GET``` /Contato/obterPorNome|Consulta contatos por nome.|
|```PUT``` /Contato/{id}|Atualiza um contato.|
|```DELETE``` /Contato/{id}|Exclui um contato.|

## Explicando o código mais a fundo

### Classe `AgendaContext`

A classe `AgendaContext` é responsável pela conexão com o banco de dados e mapeamento das entidades para tabelas. Herda de `DbContext` do Entity Framework Core. Possui um `DbSet<Contato>` para representar a tabela de contatos.

```csharp
using Microsoft.EntityFrameworkCore;
using ModuloAPI.Entities;

namespace ModuloAPI.Context
{
    public class AgendaContext : DbContext
    {
        public AgendaContext(DbContextOptions<AgendaContext> options) : base(options)
        {
        }

        public DbSet<Contato> Contatos { get; set; }
    }
}
```

### Classe `Contato`

A classe `Contato` representa uma entidade de contato. Possui as propriedades `Id`, `Nome`, `Telefone` e `Ativo`, que mapeiam os campos da tabela de contatos no banco de dados.

```csharp
namespace ModuloAPI.Entities
{
    public class Contato
    {
        public int Id { get; set; }
        public string Nome { get; set; }
        public string Telefone { get; set; }
        public bool Ativo { get; set; }
    }
}
```

### Classe `ContatoController`
```csharp
using Microsoft.AspNetCore.Mvc;
using ModuloAPI.Context;
using ModuloAPI.Entities;
using System.Linq;

namespace ModuloAPI.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class ContatoController : ControllerBase
    {
```

1. **Namespaces e Atributos**: 
   - `using Microsoft.AspNetCore.Mvc;`: Este namespace contém as classes necessárias para criar controladores da Web API.
   - `using ModuloAPI.Context;`: Importa o namespace que contém a classe `AgendaContext`, responsável pela interação com o banco de dados.
   - `using ModuloAPI.Entities;`: Importa o namespace que contém a classe `Contato`, que representa a entidade de contato.
   - `[ApiController]`: Atributo que indica que a classe é um controlador de API. Ele habilita comportamentos específicos para APIs, como a inferência automática de binding de parâmetros de ação e a validação automática do modelo de requisição.
   - `[Route("[controller]")]`: Define a rota base para todas as ações do controlador. Neste caso, `[controller]` será substituído pelo nome do controlador, ou seja, `Contato`.

```csharp
        private readonly AgendaContext _context;
        
        public ContatoController(AgendaContext context)
        {
            _context = context;
        }
```

2. **Construtor**:
   - `private readonly AgendaContext _context;`: Declaração de uma variável privada para armazenar uma instância de `AgendaContext`.
   - `public ContatoController(AgendaContext context)`: Construtor da classe `ContatoController`. Recebe uma instância de `AgendaContext` através da injeção de dependência para acesso ao banco de dados.

```csharp
        [HttpPost]
        public IActionResult Create(Contato contato)
        {
            _context.Add(contato);
            _context.SaveChanges();
            return CreatedAtAction(nameof(ObterId), new { id = contato.Id }, contato);
        }
```

3. **Método `Create` (HttpPost)**:
   - Método responsável por criar um novo contato no banco de dados.
   - `HttpPost`: Define que este método responde a requisições HTTP do tipo POST.
   - `ActionResult`: Tipo de retorno que representa um resultado de uma ação. Neste caso, pode ser `CreatedAtAction` (código 201) se o contato for criado com sucesso.
   - `CreatedAtAction(nameof(ObterId), new { id = contato.Id }, contato)`: Cria uma resposta com o status HTTP 201 (Created) e um cabeçalho de localização contendo a URL do recurso recém-criado.

```csharp
        [HttpGet("{id}")]
        public IActionResult ObterId(int id)
        {
            var contato = _context.Contatos.Find(id);
            return contato != null ? Ok(contato) : NotFound();
        }
```

4. **Método `ObterId` (HttpGet)**:
   - Método para obter um contato pelo seu identificador.
   - `HttpGet("{id}")`: Define que este método responde a requisições HTTP GET na rota `{id}`, onde `id` é um parâmetro de rota.
   - `ActionResult`: Tipo de retorno que representa um resultado de uma ação. Neste caso, pode ser `Ok` (código 200) se o contato for encontrado ou `NotFound` (código 404) se não for encontrado.

```csharp
        [HttpGet("obterPorNome")]
        public IActionResult ObterNome(string nome)
        {
            var contatos = _context.Contatos.Where(x => x.Nome.Contains(nome));
            return Ok(contatos);
        }
```

5. **Método `ObterNome` (HttpGet)**:
   - Método para obter contatos por nome.
   - `HttpGet("obterPorNome")`: Define que este método responde a requisições HTTP GET na rota `obterPorNome`.
   - `ActionResult`: Tipo de retorno que representa um resultado de uma ação. Neste caso, sempre retorna `Ok` (código 200) com uma coleção de contatos que correspondem ao nome especificado.

```csharp
        [HttpPut("{id}")]
        public IActionResult Atualizar(int id, Contato contato)
        {
            var contatoBanco = _context.Contatos.Find(id);

            if (contatoBanco == null)
            {
                return NotFound();
            }

            contatoBanco.Nome = contato.Nome;
            contatoBanco.Telefone = contato.Telefone;
            contatoBanco.Ativo = contato.Ativo;

            _context.Contatos.Update(contatoBanco);
            _context.SaveChanges();

            return Ok(contatoBanco);
        }
```

6. **Método `Atualizar` (HttpPut)**:
   - Método para atualizar os dados de um contato existente.
   - `HttpPut("{id}")`: Define que este método responde a requisições HTTP PUT na rota `{id}`, onde `id` é um parâmetro de rota.
   - `ActionResult`: Tipo de retorno que representa um resultado de uma ação. Neste caso, pode ser `Ok` (código 200) se o contato for atualizado com sucesso ou `NotFound` (código 404) se não for

 encontrado.

```csharp
        [HttpDelete("{id}")]
        public IActionResult Deletar(int id)
        {
            var contatoBanco = _context.Contatos.Find(id);

            if (contatoBanco == null)
            {
                return NotFound();
            }

            _context.Contatos.Remove(contatoBanco);
            _context.SaveChanges();
            return NoContent();
        }
    }
}
```

7. **Método `Deletar` (HttpDelete)**:
   - Método para excluir um contato do banco de dados.
   - `HttpDelete("{id}")`: Define que este método responde a requisições HTTP DELETE na rota `{id}`, onde `id` é um parâmetro de rota.
   - `ActionResult`: Tipo de retorno que representa um resultado de uma ação. Neste caso, retorna `NoContent` (código 204) se o contato for excluído com sucesso ou `NotFound` (código 404) se não for encontrado.

