# GamePlease: plano de estudos full stack

Objetivo: sair de dev Flutter sênior (5 anos) para full stack (Java/Spring + iOS nativo + Android nativo) o mais rápido possível, usando o GamePlease como laboratório. O projeto não será publicado, então o critério de sucesso é aprender e ter um portfólio defensável, não lançar.

## 0. Premissas (edite se estiverem erradas)

- Disponibilidade: ~15 h/semana (2 h por dia útil + 5 h no fim de semana). Se for menos, veja a seção 7.
- Você tem um Mac com Xcode e Android Studio instalados.
- Rodízio de semanas: Backend, iOS, Android, repetido 3 vezes (9 semanas) + 1 semana de buffer/portfólio.
- Cada ciclo é uma **fatia vertical**: o backend entrega uma funcionalidade na semana B, e iOS e Android a consomem nas duas semanas seguintes. Assim o mesmo contrato de API é implementado nas duas plataformas, e você compara as duas.
- Escopo reduzido para aprender (diferente do MVP "real"): sem custódia obrigatória de chaves, sem upload de imagem, sem moderação de loja, sem monetização.

## 1. Como a bagagem Flutter se traduz

| Flutter | SwiftUI | Compose | Backend (Java/Spring) |
|---|---|---|---|
| Widget | View | @Composable | n/a |
| setState / Provider / Bloc | @State, @Observable, ViewModel | ViewModel + StateFlow | n/a |
| Future / Stream | async/await, AsyncSequence | suspend, Flow | CompletableFuture (raro; Spring MVC é bloqueante por padrão) |
| Navigator 2 / go_router | NavigationStack | Navigation Compose | n/a |
| Dio + json_serializable | URLSession + Codable | Retrofit + kotlinx.serialization/Moshi | RestController + Jackson |
| get_it / injectable | injeção manual / ambiente | Hilt | IoC do Spring (@Component, @Bean) |
| pubspec | Swift Package Manager | Gradle | Maven/Gradle |

Consequência: as semanas de iOS e Android têm curva menor para você do que parecem (UI declarativa, async e arquitetura você já domina). **O backend é a lacuna real**, então ele recebe mais profundidade por semana e uma manutenção diária nas semanas de mobile.

## 2. Regras de estudo com a IA

A IA é professora e revisora. Você escreve o código.

- **Antes:** peça para a IA explicar o conceito e fazer perguntas. Skills úteis (instaladas em `.claude/skills`): `teach`, `grill-me`, `domain-modeling`.
- **Durante:** trave por 30 min antes de pedir ajuda. Escalonamento: (1) nome do conceito, (2) direção, (3) trecho mínimo. Nunca peça a feature inteira.
- **Depois:** peça revisão do seu diff (`code-review`) e peça perguntas difíceis sobre o que você escreveu.
- **Testes:** escreva os seus (`tdd` ajuda no ritmo red-green-refactor). A IA sugere casos que você esqueceu.
- **Prova real:** ao fim de cada semana, explique em voz alta, sem consultar, a lista "Explique de cabeça".

## 3. Rotina

**Diária (~2 h):**
1. 10 min: explique em voz alta o que fez ontem (ou responda 3 perguntas da lista da semana).
2. 90 min: construção focada, sem IA escrevendo código.
3. 20 min: rode os testes, faça commit pequeno, anote 3 linhas no diário (o que aprendi / o que errei / dúvida).

**Semanal:**
- **Segunda:** planejamento. Defina o entregável e rascunhe o modelo ou as telas à mão. Peça à IA que questione o plano.
- **Quarta:** checkpoint. Compare com o critério de "feito" e corte escopo se estiver atrasado.
- **Sexta:** revisão de código com a IA + demo para você mesmo (grave a tela) + retro de 10 min.
- **Fim de semana:** bloco longo para o item mais difícil da semana.

**Manutenção do backend nas semanas de mobile (30–45 min/dia):** um exercício de SQL, ler um trecho da documentação do Spring ou Postgres, e escrever um teste ou um endpoint pequeno. O backend não pode esfriar enquanto você está em Swift ou Kotlin.

## 4. Cronograma

### Ciclo 1: fatia "jogos + wishlist"

**Semana 1, Backend: fundamentos**
- Java 21 para quem vem de Dart: records, generics, collections, streams, Optional, exceções, equals/hashCode, imutabilidade.
- Spring Boot: IoC/DI, camadas (controller, service, repository), DTOs, validação (Bean Validation), tratamento de erros (ProblemDetail).
- PostgreSQL + Spring Data JPA + Flyway; relações e N+1; Docker Compose para o banco.
- JUnit 5 + Testcontainers.
- Entregável: busca de jogos (tabela com seed) e CRUD de wishlist, com migrations, testes de integração e OpenAPI. Regra 1 ("sem desejo ativo duplicado") com índice único parcial.
- Explique de cabeça: o que é inversão de controle; ciclo de vida de uma requisição no Spring; por que usar DTO em vez de expor a entidade; o que é N+1 e como evitar; o que a migration garante.
- Feito quando: `docker compose up` + `./mvnw test` passam do zero, sem passos manuais.

**Semana 2, iOS: fundamentos**
- Swift: optionals, structs vs classes, enums com associated values, protocols, ARC, Codable.
- SwiftUI: composição, `@State`/`@Observable`, `NavigationStack`, listas.
- Concorrência: async/await, `Task`, `MainActor`. Rede com URLSession.
- MVVM com repositório e ViewModel testável (XCTest com fake).
- Entregável: busca de jogos, lista de wishlist, adicionar e remover.
- Explique de cabeça: value vs reference semantics; o que o `MainActor` garante; como o SwiftUI decide re-renderizar.

**Semana 3, Android: fundamentos**
- Kotlin: null safety, data/sealed classes, extension functions, coroutines, Flow/StateFlow.
- Compose: estado, side-effects, recomposição, Navigation Compose.
- Hilt, Retrofit, ViewModel; testes com JUnit e fakes.
- Entregável: as mesmas telas da semana 2.
- Explique de cabeça: o que dispara recomposição; `StateFlow` vs `SharedFlow`; escopos de coroutine; como Hilt resolve dependências.
- Comparação: escreva meia página sobre o que foi mais fácil ou difícil em cada plataforma frente ao Flutter.

### Ciclo 2: fatia "autenticação + feed + reserva"

**Semana 4, Backend: segurança e concorrência**
- Spring Security: filtros, BCrypt, JWT de acesso + refresh, autorização por dono do recurso.
- Feed com paginação (cursor vs offset) e filtros.
- Reserva: transações, níveis de isolamento, locking otimista (`@Version`) vs índice único parcial, expiração (job agendado ou checagem na leitura).
- Teste de concorrência: duas threads reservando o mesmo desejo, e só uma vence.
- Explique de cabeça: por que JWT é stateless e quais problemas isso traz; o que acontece se duas reservas chegam juntas; diferença entre autenticação e autorização; como o refresh token é revogado.
- Feito quando: o teste de concorrência é determinístico e passa em 50 execuções seguidas.

**Semana 5, iOS:** login, armazenamento do token no Keychain, renovação do token, feed paginado, fluxo de reserva, estados de erro e carregamento. Testes de ViewModel.

**Semana 6, Android:** os mesmos fluxos, token no Keystore/DataStore criptografado, paginação, interceptor de autenticação do Retrofit. Testes de ViewModel.

### Ciclo 3: fatia "presente + segurança + operação"

**Semana 7, Backend: domínio e produção**
- Máquina de estados do presente, separada do estado do desejo.
- `GiftSecret`: criptografia AES-GCM com chave mestra vinda do ambiente (envelope encryption como estudo), e nada de segredo em log.
- Audit log, denúncia, bloqueio, rate limiting.
- Logs estruturados, Actuator, health checks.
- GitHub Actions (build + testes), Dockerfile, deploy em um serviço gratuito para ter uma URL real.
- Opcional: login pela Steam (OpenID).
- Explique de cabeça: por que GCM e não ECB; por que não logar o corpo da requisição; o que a máquina de estados impede; como testar o job de expiração.

**Semana 8, iOS:** fluxo de presente e revelação da chave (tela que oculta conteúdo no snapshot), denúncia e bloqueio, acessibilidade básica, testes de UI, noções de Instruments. Push é opcional (APNs exige conta paga de desenvolvedor; use notificação local se não tiver).

**Semana 9, Android:** os mesmos fluxos, `FLAG_SECURE` na tela da chave, testes de UI, perfil com Android Studio Profiler. FCM é opcional (gratuito).

**Semana 10, Buffer e portfólio**
- README com arquitetura, diagrama e como rodar; ADRs com as decisões; coleção/OpenAPI; vídeo curto de demo.
- Revisão final de dívida técnica; CI para os dois apps.
- Preparação de entrevista (veja a seção 6).

## 5. Referências para começar

Pontos de partida, não uma lista exaustiva. Confira se continuam atuais.

- **Java/Spring:** documentação e guias oficiais do Spring; Baeldung; *Effective Java* (Bloch); documentação do PostgreSQL.
- **Swift:** *The Swift Programming Language* (Apple), tutoriais "Develop in Swift", 100 Days of SwiftUI (Hacking with Swift).
- **Kotlin/Compose:** documentação do Kotlin e os caminhos oficiais de Jetpack Compose em developer.android.com.

## 6. Como saber que virou full stack

Checklist de saída:

- [ ] Sei desenhar um modelo relacional, escrever migration e explicar um plano de consulta (`EXPLAIN`).
- [ ] Implemento autenticação e autorização no backend e sei apontar suas falhas comuns.
- [ ] Reproduzo e corrijo uma condição de corrida com teste.
- [ ] Os dois apps consomem a mesma API e têm testes de ViewModel.
- [ ] CI roda tudo; o backend tem URL pública de demo.
- [ ] Consigo defender cada decisão de arquitetura (ADRs) numa conversa de 20 minutos.

Preparação de entrevista (na semana 10): perguntas clássicas de Java (memória, coleções, concorrência), SQL (joins, índices, transações), design de API REST, e um system design pequeno (por exemplo, "projete o fluxo de reserva sem duplicidade").

**Expectativa honesta:** em 9 a 10 semanas você fica credível como dev mobile que também entrega backend e consegue conversar com um time full stack. Profundidade de backend sênior (observabilidade em escala, mensageria, tuning de banco) leva mais tempo. Use o portfólio para provar o que você já sabe e liste o resto como próximo estudo.

## 7. Riscos do plano e o que cortar

- **Uma semana por stack é curta.** O rodízio ajuda a manter o ritmo, mas esquecer é o risco. A manutenção diária do backend e a repetição do mesmo contrato nas duas plataformas existem para compensar.
- **Se tiver menos de 15 h/semana:** corte nesta ordem (1) push, (2) Steam OpenID, (3) segunda plataforma do ciclo 3, (4) denúncia e bloqueio.
- **Se atrasar no backend:** não avance de ciclo. Pague a semana com o buffer; os apps dependem da API.
- **Tentação de deixar a IA gerar:** se você abriu a semana sem conseguir explicar de cabeça o que fez, repita o bloco, não o ignore.
