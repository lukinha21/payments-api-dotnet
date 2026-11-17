# 💳 API de Pagamentos (.NET 8)

API em **ASP.NET Core (.NET 8)** para simular o fluxo de **pagamento com cartão**: identificar a bandeira, validar o cartão, calcular parcelas com juros e controlar a situação de uma transação (pendente, confirmada ou cancelada). Usa **Entity Framework Core com MySQL**, documentação **Swagger** e logs com **Serilog** (arquivo JSON e tabela no MySQL).

Projeto desenvolvido em aula na Unoeste.

![.NET](https://img.shields.io/badge/.NET-8-512BD4?logo=dotnet&logoColor=white)
![C#](https://img.shields.io/badge/C%23-239120?logo=csharp&logoColor=white)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET_Core-Web_API-512BD4?logo=dotnet&logoColor=white)
![Entity Framework Core](https://img.shields.io/badge/EF_Core-8-512BD4)
![MySQL](https://img.shields.io/badge/MySQL-Pomelo-4479A1?logo=mysql&logoColor=white)
![Swagger](https://img.shields.io/badge/Swagger-OpenAPI-85EA2D?logo=swagger&logoColor=black)
![Serilog](https://img.shields.io/badge/Serilog-logs-1E1E1E)

## ✨ Funcionalidades (camada de serviços)

**Cartões (`CartaoService`)**
- **Identificação da bandeira** a partir do número (13 dígitos), usando os 4 primeiros dígitos e o 8º dígito:
  `1111...1` → VISA · `2222...2` → MASTERCARD · `3333...3` → ELO.
- **Validação do cartão**: verifica se o cartão existe no banco e se a validade ainda não venceu.

**Transações (`TransacaoService`)**
- **Cálculo de parcelas**: aplica a taxa de juros sobre o valor total e divide pelo número de parcelas.
- **Criação de pagamento**: grava a transação com situação **Pendente**.
- **Consulta da situação** de uma transação.
- **Confirmação** e **cancelamento**: só são permitidos enquanto a transação está pendente.

**Infraestrutura**
- `PagamentoDbContext` com as entidades `Cartao` e `Transacao` (MySQL via Pomelo).
- Swagger na página inicial da API, com os comentários XML do código.
- Serilog gravando logs em `logs/logs.json` e erros na tabela `Logs` do MySQL.
- Classe de middleware para tratamento de exceções (`Middlewares/`), ainda não registrada no `Program.cs`.

## 🧩 Modelo de dados

| Entidade | Campos |
|---|---|
| `Cartao` | `Numero` (chave), `Validade` |
| `Transacao` | `TransacaoId`, `Valor`, `Cartao`, `Cvv`, `Parcelas`, `Situacao` |
| `SituacaoPagamento` (enum) | `Pendente = 1`, `Confirmado`, `Cancelado` |

## 📁 Estrutura do projeto

```
├── Data/PagamentoDbContext.cs
├── Domain/                 # Cartao, Transacao
├── DTOs/                   # TransacaoRequest, CalculoParcelasRequest
├── Enum/SituacaoPagamento.cs
├── Middlewares/ExceptionHandlingMiddleware.cs
├── Services/               # CartaoService, TransacaoService + interfaces
├── Program.cs              # DI, EF Core, Serilog e Swagger
├── appsettings.json
└── Pagamentos.csproj
```

## ▶️ Como executar

Pré-requisitos: **.NET 8 SDK** e um **MySQL**.

1. Defina a string de conexão do MySQL. O `appsettings.json` traz apenas um exemplo (`SEU_USUARIO`/`SUA_SENHA`); para não versionar senhas, prefira a variável de ambiente `stringConexao`, que tem prioridade sobre o arquivo:
   ```bash
   # Linux/macOS
   export stringConexao="Server=localhost;Database=pagamentos;Uid=SEU_USUARIO;Pwd=SUA_SENHA;"
   # Windows (PowerShell)
   $env:stringConexao="Server=localhost;Database=pagamentos;Uid=SEU_USUARIO;Pwd=SUA_SENHA;"
   ```
2. Crie as tabelas `Cartao` e `Transacao` (e a tabela `Logs`, usada pelo Serilog).
3. Rode a API:
   ```bash
   git clone https://github.com/lukinha21/payments-api-dotnet.git
   cd payments-api-dotnet
   dotnet run
   ```
4. Abra a URL exibida no terminal (ex.: `http://localhost:5255`): o Swagger fica na página inicial.

## 🚧 Status

A camada de serviços, o modelo de dados e a configuração estão neste repositório. Os controllers HTTP (`CartoesController` e `PagamentosController`), que expõem essas operações, ainda serão adicionados ao repositório.

## 👨‍💻 Autor

**Lucas Alexandre**, estudante de Sistemas de Informação na Unoeste

- GitHub: [@lukinha21](https://github.com/lukinha21)
- LinkedIn: [Lucas Silva](https://www.linkedin.com/in/lucas-silva-025583180)
