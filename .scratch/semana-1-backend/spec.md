# Semana 1, Backend: fundamentos (busca de jogos + CRUD de wishlist)

Status: ready-for-human

## Problem Statement

Quem vem de Flutter (5 anos) não tem, hoje, nenhum backend Java/Spring para sustentar os apps iOS e Android das semanas 2 e 3. Os apps precisam de duas capacidades mínimas: **encontrar jogos** em um catálogo e **manter uma wishlist** de jogos desejados. Sem um contrato de API estável e testado, as duas semanas de mobile construiriam em cima de suposições.

Do ponto de vista de quem estuda, o problema é outro: o backend é a lacuna real do plano, e a semana 1 só vale se cada conceito (IoC, camadas, DTO, N+1, migrations, concorrência) for **visto funcionando** e depois **explicado de cabeça**, não só copiado.

## Solution

Um monólito modular com dois módulos, `game` e `wishlist`, expostos por uma API HTTP:

- O jogador busca jogos por nome em um catálogo populado por seed.
- O jogador adiciona, lista, consulta, edita e remove desejos na sua wishlist.
- A Regra 1 do produto ("sem desejo ativo duplicado") é garantida pelo banco, com um índice único parcial, e não por checagem na aplicação.
- O contrato é publicado em OpenAPI antes de qualquer cliente consumir.
- Qualquer pessoa clona o repositório e, com `docker compose up -d` e `./mvnw test`, vê tudo verde, sem passo manual.

## User Stories

1. Como jogador, quero buscar jogos pelo nome, para encontrar o que quero ganhar.
2. Como jogador, quero que a busca ignore maiúsculas/minúsculas e aceite parte do nome, para não depender de lembrar o título exato.
3. Como jogador, quero resultados paginados e ordenados por nome, para navegar um catálogo grande sem carregar tudo.
4. Como jogador, quero ver ano de lançamento, gêneros e capa de cada jogo, para reconhecer o jogo certo.
5. Como jogador, quero uma lista vazia (e não um erro) quando a busca não encontra nada, para entender que simplesmente não há resultado.
6. Como jogador, quero adicionar um jogo à minha wishlist, para que outras pessoas saibam que eu o desejo.
7. Como jogador, quero definir se o desejo é público ou privado ao adicioná-lo, para controlar quem o vê.
8. Como jogador, quero registrar um motivo opcional para o desejo, para dar contexto a quem for me presentear.
9. Como jogador, quero ser impedido de adicionar o mesmo jogo duas vezes enquanto houver desejo ativo, para não ter duplicatas na minha lista.
10. Como jogador, quero receber uma resposta clara (409) quando tento duplicar um desejo, para o app mostrar uma mensagem útil.
11. Como jogador, quero poder readicionar um jogo que removi antes, para mudar de ideia sem ser bloqueado pelo histórico.
12. Como jogador, quero ver a minha wishlist com o nome do jogo em cada item, para não precisar de uma segunda chamada por item.
13. Como jogador, quero ver só os desejos ativos na minha lista, para que itens removidos não poluam a tela.
14. Como jogador, quero consultar um desejo pelo identificador, para abrir o detalhe dele.
15. Como jogador, quero editar o motivo e a visibilidade de um desejo, para corrigir ou mudar a privacidade sem recriá-lo.
16. Como jogador, quero que o jogo de um desejo não possa ser trocado, para que a regra de duplicidade continue consistente (remove e adiciona outro).
17. Como jogador, quero remover um desejo, para tirá-lo da minha lista.
18. Como jogador, quero que a remoção preserve o registro (soft delete), para que o histórico exista quando o produto precisar dele.
19. Como jogador, quero receber 404 ao acessar um desejo de outra pessoa, para que a API não revele que ele existe.
20. Como jogador, quero erros de validação que apontem o campo e o problema, para o app destacar o campo errado.
21. Como jogador, quero ser impedido de adicionar um jogo que não existe no catálogo, para não criar desejos órfãos.
22. Como dev de app (iOS/Android), quero um contrato OpenAPI publicado e versionado, para gerar/escrever clientes sem adivinhar formatos.
23. Como dev de app, quero erros em `ProblemDetail` com `type` estável, para tratar cada erro sem parsear texto.
24. Como dev de app, quero que a paginação tenha envelope próprio (`items`, `page`, `size`, `totalElements`), para que o formato não dependa de uma classe interna do framework.
25. Como dev de app, quero que as rotas não carreguem o id do usuário, para que a troca do header pelo JWT (semana 4) não mude o contrato.
26. Como pessoa que estuda, quero ver o N+1 acontecer e ser medido por teste, para entender o problema em vez de decorá-lo.
27. Como pessoa que estuda, quero provar por teste concorrente que só uma de duas inserções simultâneas vence, para entender por que `exists` + `insert` não basta.
28. Como pessoa que estuda, quero que o banco seja criado só por migrations, para saber o que a migration garante e que qualquer ambiente é reproduzível.
29. Como pessoa que estuda, quero testar contra PostgreSQL real, para que o índice parcial seja exercitado de verdade.
30. Como pessoa que estuda, quero que `docker compose up -d` e `./mvnw test` passem do zero, para ter certeza de que o projeto não depende da minha máquina.
31. Como pessoa que estuda, quero escrever ADRs das decisões principais, para saber justificá-las numa entrevista.
32. Como pessoa que estuda, quero responder de cabeça às cinco perguntas do plano, para confirmar que entendi e não só que o código funciona.

## Implementation Decisions

**Ambiente e build**
- Java 21 (Temurin) fixado por SDKMAN no repositório; Maven com wrapper; última GA estável do Spring Boot, confirmada no start.spring.io no momento de gerar o projeto.
- PostgreSQL via Docker Compose, com credenciais de desenvolvimento fixas e públicas no próprio arquivo (banco local descartável, sem dado real). Segredos reais (semana 7) virão só de variável de ambiente.
- O Compose sobe **só o banco**. O Dockerfile da aplicação fica para a semana 7.
- Runtime Docker no Mac: OrbStack (ou Colima). É pré-requisito do Testcontainers.
- Schema gerenciado só por Flyway; o Hibernate apenas valida (nunca gera DDL). `open-in-view` desligado.

**Arquitetura**
- Pacotes por módulo (`game`, `wishlist`), cada um com suas camadas (controller, service, repository). O `wishlist` consulta o `game` somente por uma API pública do módulo, nunca pelo repository dele.
- Sem Lombok e sem MapStruct: DTOs são `record`, mapeamento entidade ↔ DTO é manual.
- Código, rotas, campos JSON e `type`/`title`/`detail` dos erros em inglês; documentos em pt-BR.

**Dados**
- `games`: id `bigint identity`, `steam_app_id` único, nome, ano de lançamento, gêneros (texto simples), URL da capa. Seed de ~60 jogos reais em migration versionada, gerado por um script pequeno que você escreve.
- `users`: id UUID, nome. Tabela mínima populada por migration; o id chega por header `X-User-Id` até o JWT da semana 4.
- `wishlist_items`: id UUID (preferencialmente UUIDv7), referência ao usuário e ao jogo, `status` (`ACTIVE` | `REMOVED`), `visibility` (pública | privada), `reason` opcional com limite de tamanho, `created_at` e `updated_at` (`timestamptz`, preenchidos por auditing).
- **Regra 1**: índice único parcial sobre (usuário, jogo) apenas onde `status = ACTIVE`. Remover faz soft delete; readicionar cria uma linha nova.
- O modelo cobre só o estado do **desejo**. Reserva e presente são outro modelo e ficam fora.

**API**
- `GET /games?query=&page=&size=`: filtro por nome (`ILIKE %query%`), ordenação por nome, paginação por offset, envelope próprio.
- `POST /wishlist`, `GET /wishlist` (só ativos do usuário do header), `GET /wishlist/{id}`, `PATCH /wishlist/{id}` (só `reason` e `visibility`), `DELETE /wishlist/{id}` (soft delete, 204).
- `GET /wishlist` devolve o nome do jogo em cada item. `WishlistItem` referencia `Game` por associação lazy; o N+1 é provado e depois corrigido (entity graph ou join fetch).
- **Duplicidade**: o service não faz `exists` antes do insert. Insere, captura a violação, confirma pelo nome da constraint que é a do índice parcial, e lança exceção de domínio → 409.
- **Erros**: um tratador central devolve `ProblemDetail` (RFC 9457) com `type` URI estável, `title`, `status`, `detail`. Validação traz lista `errors` com `field` e `message`. Item de outro usuário → 404. Mensagens nunca ecoam o valor recebido.
- **Contrato**: OpenAPI gerado do código (springdoc), `openapi.json` commitado, e um teste que falha se o arquivo versionado divergir do gerado.

## Testing Decisions

**O que é um bom teste aqui:** exercita comportamento externo observável (resposta HTTP, estado do banco, contagem de queries), nunca detalhe interno como "o método X foi chamado". Nada de mock de repository.

**Pontos de teste (seams):**
1. **Principal, a API HTTP** (`@SpringBootTest` + MockMvc, ou `RestTestClient` se a versão do Boot tiver) contra PostgreSQL real. Cobre os fluxos, os erros, o 404 alheio, o N+1 (contagem de queries via Hibernate Statistics) e a concorrência da Regra 1.
2. **Repository** (`@DataJpaTest`), só onde o comportamento é do banco: o índice parcial e o caso "remove e readiciona".
3. **Unidade pura**, só para lógica com regra própria (por exemplo, a normalização do termo de busca).

**Infraestrutura:** Testcontainers com `@ServiceConnection` e um container compartilhado entre as classes. Sem H2.

**Testes obrigatórios para fechar a semana:** N+1 (contagem de queries), concorrência da Regra 1 (duas inserções simultâneas, só uma vence; estável em 20 execuções seguidas) e divergência do `openapi.json`.

**Prior art:** nenhum, é o primeiro módulo. Estes testes definem o padrão para as semanas seguintes (a semana 4 reaproveita o de concorrência).

**Autoria:** você escreve os testes; a IA revisa.

## Out of Scope

- Login, JWT e Spring Security (semana 4). Aqui só existe o header temporário.
- Feed de desejos públicos, paginação por cursor, reserva, presente, chave, denúncia, bloqueio e auditoria.
- Dockerfile da aplicação, CI (GitHub Actions), deploy e Actuator (semana 7).
- Índice `pg_trgm` ou busca full-text: com ~60 jogos não há o que medir. Fica como "mede e decide depois".
- Cache, virtual threads, Lombok, MapStruct.
- Fonte real do catálogo (Steam): o seed local basta.

## Further Notes

**Definição de pronto**
- `docker compose up -d` e `./mvnw test` passam do zero, em clone limpo, sem passo manual.
- Os três testes obrigatórios estão verdes.
- Sessão oral no chat com as cinco perguntas do plano, sem consulta: inversão de controle, ciclo de vida de uma requisição no Spring, DTO em vez de entidade, N+1 e como evitar, o que a migration garante. A IA aprofunda cada resposta; lacunas viram anotação para revisar antes da semana 4.
- Quatro ADRs escritos por você: soft delete + índice parcial; sem pré-checagem de duplicidade; OpenAPI code-first; `bigint` vs `UUID`.

**Detalhes em aberto (decidir no ticket indicado, registrar a decisão no próprio ticket)**
- Tamanho máximo do `reason` e valor padrão de `visibility` (ticket 03).
- Header `X-User-Id` ausente, malformado ou de usuário inexistente: qual status e qual `type` (ticket 03).
- Biblioteca de UUIDv7, ou cair para v4 (ticket 03).
- Termo de busca vazio: devolve o catálogo todo paginado? (ticket 02).

**Processo:** uma branch por fatia, merge local em `main` quando a fatia passa nos testes e na revisão. Commits só quando você pedir. Os tickets estão em `issues/`; o plano de fundo é o `STUDY_PLAN.md`.
