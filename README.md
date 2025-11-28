# cursor-ide

Repositório de regras e convenções para usar o Cursor IDE em projetos **Java (Spring Boot)** e **Python (Falcon)**, com foco em arquitetura limpa, testes robustos e boas práticas de engenharia.

## Visão geral

Este projeto não contém código de aplicação em si: ele centraliza arquivos `.cursorrules` que ensinam a IA do Cursor a:

- Seguir um **estilo de arquitetura e código bem definido** para Java e Python.
- Respeitar convenções de camadas, pacotes, testes, logging e integração com serviços externos.
- Ajudar você a gerar código alinhado com os padrões da sua equipe, sem precisar repetir instruções em cada conversa.

Atualmente há dois conjuntos principais de regras:

- `java/.cursorrules`: para serviços Java com Spring Boot.
- `python/.cursorrules`: para APIs Python usando Falcon.

## Estrutura do repositório

```text
cursor-ide/
├── java/
│   └── .cursorrules      # Regras de IA para projetos Java / Spring Boot
├── python/
│   └── .cursorrules      # Regras de IA para projetos Python / Falcon
└── README.md
```

## Regras para projetos Java (`java/.cursorrules`)

### Stack e arquitetura

- Java 17 com Spring Boot **3.5.5**.
- Arquitetura em três camadas seguindo DDD:
  - `application`: `config`, `controller`, `model`, `validation` e a classe `Application`.
  - `domain`: `exceptions`, `messaging`, `model`, `repository`, `service`, `validation` com regras de negócio, entidades, VOs e serviços de domínio.
  - `infrastructure`: `configuration`, `external`, `repository` com integrações, implementação de repositórios e detalhes de framework.
- Separação rígida de responsabilidades: **negócio sempre no domínio**, controllers apenas orquestram HTTP e delegam para application/domain.

### Padrões de código

- Convenções de nomenclatura: `PascalCase` para classes, `camelCase` para variáveis/métodos, `UPPER_SNAKE_CASE` para constantes.
- Uso consistente de validação com Bean Validation (`@Valid`, validadores customizados).
- Configuração via `@ConfigurationProperties` e perfis (`integration-test`, etc.).

### Testes e qualidade

- Testes:
  - Unitários com **JUnit 5**.
  - BDD com **Cucumber**.
  - Testes de integração com `@SpringBootTest`, **Testcontainers** e **LocalStack** (AWS: SNS, SQS, S3, DynamoDB), além de containers dedicados para Redis e MongoDB.
  - Stubs de HTTP com **WireMock**.
- Qualidade:
  - Cobertura com **JaCoCo** com meta mínima de **95%** (com alguns pacotes excluídos).
  - Aplicação sistemática de princípios **SOLID** e **DRY**, com checklists de antes/durante/depois de implementar.

### Observabilidade e infraestrutura

- Logging com **SLF4J + Logback**, logging estruturado e `MemoryAppender` para verificação em testes.
- Monitoramento com **Spring Boot Actuator**.
- Integração com **AWS SDK v2**, **Spring Cloud**, Redis, SQS/SNS, etc.

## Regras para projetos Python (`python/.cursorrules`)

### Stack e arquitetura

- APIs em **Python** usando **Falcon** (alto desempenho, WSGI).
- Arquitetura típica de sistema de autorização de transações, com camadas bem definidas:
  - `/api/`: resources Falcon.
  - `/client/`: clientes HTTP externos.
  - `/common/`: utilitários, middleware, logging, validators.
  - `/domain/data/model/`: modelos de dados para requests/responses.
  - `/enums/`, `/repository/`, `/service/`, `/util/`: enums, acesso a dados com PynamoDB, serviços de negócio e utilitários.

### Padrões de código

- Preferência por funções (programação funcional/declarativa), exceto onde classes são necessárias (resources Falcon, services, clients, models).
- Convenções:
  - Arquivos e módulos em `lowercase_with_underscores`.
  - Classes em `PascalCase`, métodos em `snake_case`.
  - Variáveis com nomes descritivos (`is_active`, `has_permission`).
- Uso de type hints em todas as funções sempre que possível.

### Integrações e dados

- **PynamoDB** para acesso ao DynamoDB, com:
  - Nomes de tabela por ambiente.
  - TTL para expiração automática.
  - Padrão de repositório com métodos estáticos.
- Integrações HTTP com clients especializados, logging estruturado e tratamento robusto de erros.
- Uso extensivo de **JWT** com PyJWT, validação de headers `TRAFFIC-*` e contexto de segurança centralizado.

### Testes e observabilidade

- Testes unitários com **pytest** e cliente de teste do Falcon para integração.
- Fixtures para app, banco e clients externos.
- Logging estruturado (ex.: `structlog`) com categorias (`logCategory`) padronizadas.

## Como usar estas regras no Cursor IDE

1. **Clone este repositório** ao configurar seu ambiente de desenvolvimento:
   ```bash
   git clone https://github.com/seu-usuario/cursor-ide.git
   ```
2. **Copie o arquivo de regras adequado** para o root do seu projeto:
   - Para um serviço Java:
     - Copie `java/.cursorrules` para o diretório raiz do serviço Java.
   - Para uma API Python:
     - Copie `python/.cursorrules` para o diretório raiz da API Falcon.
3. **Abra o projeto no Cursor IDE**:
   - O Cursor lerá o `.cursorrules` e passará a seguir automaticamente essas convenções nas respostas da IA.
4. **Personalize se necessário**:
   - Ajuste tecnologias (por exemplo, versões de Spring Boot ou libs Python).
   - Adicione regras específicas do seu domínio (nome de pacotes, diretórios, padrões de DTO, etc.).

## Convenções de Git e mensagens de commit

As regras de IA também definem um padrão para commits:

- Mensagens de commit **sempre em Português do Brasil (pt-BR)**.
- Padrão **Conventional Commits**:
  - `feat`, `fix`, `chore`, `refactor`, `docs`, `test`, `ci`, `build`, `perf`, `style`.
- Voz no imperativo, clara e concisa.
- Resumo com no máximo **72 caracteres**.
- Formato recomendado:

```text
tipo(escopo opcional): resumo curto em pt-BR

- Detalhes adicionais em pt-BR (opcional, com marcadores)
- Contexto, motivação e efeitos colaterais (se houver)

BREAKING CHANGE: descrição em pt-BR (se aplicável)

Refs: #<id-issue> (opcional)
```

Além disso, as regras instruem a IA a **nunca executar `git add`, `git commit` ou `git push` sem autorização explícita do usuário**.

## Quando atualizar este repositório

Atualize os arquivos `.cursorrules` quando:

- Você adotar **novas versões de frameworks ou linguagens** (por exemplo, migrar de Java 17 para Java 21).
- O time fechar **novos padrões de arquitetura** ou de organização de pastas.
- Você quiser adicionar **regras específicas de domínio** (nome de contextos, padrões de eventos, políticas de segurança etc.).

Assim, o Cursor passa a refletir continuamente a forma como a sua equipe realmente trabalha. 