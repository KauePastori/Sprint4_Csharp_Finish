
# Arquitetura — Sprint3WinForms

Abaixo estão os **diagramas Mermaid** (renderizam nativamente no GitHub).

## 1) Diagrama de Contexto (C4: System Context)
```mermaid
C4Context
title Contexto: API Apostadores
Person(teacher, "Professor/Usuário", "Avalia e usa Swagger/Postman")
System_Boundary(api, "Sprint3WinForms API") {
  System(api_core, "ASP.NET Core API", "CRUD + LINQ + Swagger")
  SystemDb(db, "SQLite", "Persistência via EF Core")
}
Rel(teacher, api_core, "HTTP/JSON (Swagger, Postman)")
Rel(api_core, db, "EF Core", "SQLite")
```

## 2) Diagrama de Containers (C4: Containers)
```mermaid
C4Container
title Containers: Organização de componentes
System_Boundary(api, "API") {
  Container(web, "ASP.NET Core", "C#", "Controllers + Swagger")
  Container(svc, "Services", "C#", "Regras de negócio simples")
  ContainerDb(db, "SQLite", "Arquivo .db")
}
Rel(web, svc, "Chamadas internas")
Rel(svc, db, "EF Core (DbContext)")
```

## 3) Diagrama Lógico (Camadas/Fluxo)
```mermaid
flowchart LR
A[Request HTTP] --> B[Controller]
B --> C[Service]
C --> D[(DbContext/SQLite)]
B --> E[Swagger UI]
D --> C
C --> B
B --> A
```

## 4) Modelo de Dados (ER)
```mermaid
erDiagram
    APOSTADOR {
        int Id PK
        string Nome
        decimal Saldo
        datetime DataCadastro
    }
```

## 5) Sequência — Exemplo GET /api/Apostadores/top
```mermaid
sequenceDiagram
  participant U as Postman/Swagger
  participant C as ApostadoresController
  participant S as ApostadoresService
  participant D as AppDbContext/SQLite

  U->>C: GET /api/Apostadores/top?minSaldo&take
  C->>S: ObterTop(minSaldo, take)
  S->>D: LINQ (Where + OrderByDescending(double(Saldo)) + Take)
  D-->>S: Lista ordenada
  S-->>C: IEnumerable<Apostador>
  C-->>U: 200 OK (JSON)
```

## Decisões Importantes

- **SQLite** escolhido pela simplicidade e portabilidade.
- **Ordenação por `decimal`**: conversão para `double` **apenas** no `OrderBy` para compatibilidade com SQLite.
- **Swagger** ativado sempre (ambiente acadêmico/demonstração).
