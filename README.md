
# Sprint3WinForms (API ASP.NET Core + EF Core)

> API Web **ASP.NET Core 8** com **Entity Framework Core (SQLite)**, **Swagger/OpenAPI** e **consultas LINQ** para o domínio de **Apostadores**.  
> (Sprint 4) com documentação, diagramas e coleção Postman.

---

## ✨ Destaques

- **CRUD completo** de `Apostadores` (Create/Read/Update/Delete)
- **Swagger** com UI e documentação automática
- **EF Core (SQLite)** com _seed_ inicial e `DbContext` organizado
- **Consultas LINQ** (filtros, ordenações, agregações)
- **Camadas limpas**: `Api` (Controllers/DTOs), `Models` (Entidades/DbContext), `Services` (regras simples)
- README completo + diagramas Mermaid
- **Coleção Postman** para testes rápidos

---

## 🔧 Stack Técnica

- **.NET**: .NET 8 (Windows/Desktop por causa do host integrado do WinForms)
- **Web**: ASP.NET Core Minimal Hosting / Controllers
- **Persistência**: EF Core + **SQLite** (arquivo local)
- **Documentação**: Swagger/OpenAPI (Swashbuckle)
- **Testes manuais**: Postman (coleção incluída)
- **Diagramas**: Mermaid

---

## 🗂 Estrutura das Pastas (projeto final enxuto)

```
Sprint3WinForms/
├─ Api/
│  ├─ Controllers/
│  │  └─ ApostadoresController.cs
│  ├─ DTOs/ (opcional)
│  └─ Program.cs (hosting + Swagger + DI)
├─ Models/
│  ├─ Apostador.cs
│  └─ AppDbContext.cs
├─ Services/
│  └─ ApostadoresService.cs
├─ appsettings.json
├─ Sprint3WinForms.csproj
└─ docs/
   ├─ architecture.md
   └─ diagrams.mmd
```

> **Importante**: se você recebeu a solução com projetos de exemplo/WinForms, este repositório já está **limpo** para focar na **API**. O host do Swagger sobe normalmente via Kestrel.

---

## ▶️ Como Executar

### 1) Pré-requisitos
- **.NET SDK 8.0+**
- Windows/macOS/Linux (recomendado Windows pelo setup original)

### 2) Restaurar e Rodar
No diretório do projeto **`Sprint3WinForms`**:

```bash
dotnet restore
dotnet build
dotnet run
```

O console exibirá algo como:

```
info: Microsoft.Hosting.Lifetime[14]
      Now listening on: http://localhost:5199
```

- Acesse o **Swagger**: `http://localhost:5199/swagger`  
  (A porta pode variar; use a porta mostrada no console ou em `launchSettings.json`).

### 3) Banco de Dados
- **SQLite** em arquivo (`app.db`) criado automaticamente no diretório de execução.
- _Seed_ inicial cria alguns `Apostadores` para facilitar testes.

> **Nota sobre SQLite + decimal**: ordenar por `decimal` diretamente no SQLite pode falhar em algumas traduções LINQ. No projeto ajustamos usando **conversão para `double`** apenas na ordenação (`OrderBy(x => (double)x.Saldo)`), mantendo o tipo `decimal` na entidade para precisão.

---

## 📚 Endpoints Principais

Base: `http://localhost:PORTA/api/Apostadores`

| Verbo | Rota | Descrição |
|------:|:-----|:----------|
| GET | `/` | Lista todos os apostadores |
| GET | `/{id}` | Detalhe por Id |
| POST | `/` | Cria novo |
| PUT | `/{id}` | Atualiza existente |
| DELETE | `/{id}` | Remove |
| GET | `/top?minSaldo=0&take=5` | **LINQ**: filtra por saldo mínimo, ordena e retorna Top N |

### Exemplos (cURL)

```bash
# Listar
curl -X GET "http://localhost:5199/api/Apostadores"

# Criar
curl -X POST "http://localhost:5199/api/Apostadores" -H "Content-Type: application/json" -d "{"nome":"Maria","saldo":120.50,"dataCadastro":"2025-01-20T00:00:00"}"

# Atualizar
curl -X PUT "http://localhost:5199/api/Apostadores/1" -H "Content-Type: application/json" -d "{"id":1,"nome":"Maria Silva","saldo":300,"dataCadastro":"2025-01-15T00:00:00"}"

# Top (LINQ)
curl -X GET "http://localhost:5199/api/Apostadores/top?minSaldo=0&take=5"
```

---

## 🔍 LINQ Demonstrado

- **Where**: filtro por saldo mínimo
- **OrderBy/ThenBy**: ordenação estável, convertendo `decimal` → `double` apenas na ordenação para compatibilidade com SQLite
- **Take**: limitar Top N
- **Select/Project**: (exemplo opcional em DTOs/anon type)
- **Any/Count**: exemplos rápidos para validações internas

Trecho ilustrativo:

```csharp
var query = _ctx.Apostadores
    .Where(a => a.Saldo >= minSaldo)
    .OrderByDescending(a => (double)a.Saldo) // compat SQLite
    .Take(take);
```

---

## 🧪 Testes com Postman

Uma **coleção Postman** acompanha este repositório (arquivo `postman/Sprint3WinForms.postman_collection.json`).  
Ela já traz:
- **Variável** `baseUrl` = `http://localhost:5199`
- Requests prontos: GET all, GET by id, POST (com body), PUT, DELETE, GET Top.

Importe a coleção no Postman: *File → Import → Raw text/Upload → selecionar JSON*.

---

## 🏗 Arquitetura (Visão Rápida)

- **Controllers** finos recebem DTOs e chamam **Services**.
- **Services** orquestram regras simples e o **DbContext**.
- **DbContext** mapeia `Apostador` e aplica _seed_.
- **Swagger** documenta e permite testar a API.

Veja os **diagramas detalhados** em [`docs/architecture.md`](docs/architecture.md).

---

## ⚙️ Configuração

`appsettings.json` (padrão):
```json
{
  "ConnectionStrings": {
    "Default": "Data Source=app.db"
  },
  "Swagger": {
    "Enable": true
  }
}
```

- Para trocar o arquivo SQLite: ajuste `"Data Source"` em `appsettings.json`.
- Para rodar em porta fixa: defina `ASPNETCORE_URLS` ou edite `launchSettings.json`.

---

## ✍️ Critérios da Sprint (Cobertura)

- ✅ **ASP.NET Core Web API + EF Core CRUD**
- ✅ **Consultas LINQ**
- ✅ **Documentação**
- ✅ **Arquitetura em diagramas**
- ✅ **Endpoints externos** (20%) — *opcional, porém feito*

---

## 📄 Licença

Uso acadêmico. Adapte livremente para fins educacionais.

---
Kauê Pastori Teixeira
Nicolas Boni
Rodrigo Viana
Enzo Sartorelli
Eduardo Nistal
