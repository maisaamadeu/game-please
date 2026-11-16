# GamePlease: visão do projeto

Plataforma social de wishlist e presentes de jogos da Steam. Jogadores publicam jogos que gostariam de ganhar; outras pessoas podem realizar o desejo. Sem loja e sem pagamentos.

**Objetivo real:** laboratório de aprendizado para virar full stack (backend, iOS e Android). Não será publicado. Veja `STUDY_PLAN.md`.

## Escopo de aprendizado

Incluído:
- Cadastro e login (JWT).
- Catálogo de jogos (seed local).
- Wishlist: adicionar, remover, motivo opcional, status.
- Feed de desejos públicos disponíveis.
- Reserva de desejo com expiração.
- Presente com máquina de estados, chave criptografada (estudo) e revelação pelo destinatário.
- Denúncia, bloqueio e auditoria.

Fora de escopo: chat, comentários, pagamentos, carteira, premium, ranking, IA, outras plataformas, upload de imagem, publicação nas lojas.

## Regras de negócio

1. Não adicionar o mesmo jogo duas vezes enquanto houver desejo ativo para ele.
2. Desejos podem ser públicos ou privados.
3. Só desejos públicos e disponíveis aparecem no feed.
4. Um desejo só tem uma reserva ativa.
5. Reservas expiram.
6. Reserva expirada sem envio devolve o desejo à disponibilidade.
7. Chaves nunca são exibidas publicamente.
8. Só o destinatário autorizado revela a chave.
9. Enviar a chave não significa que foi resgatada.
10. O destinatário confirma o resgate ou contesta.

Estado do desejo e estado do presente são separados.

## Arquitetura

- Monólito modular. Backend é a fonte única da verdade.
- Backend: Java 21, Spring Boot, Spring Security, Spring Data JPA, PostgreSQL, Flyway, Docker, JUnit + Testcontainers.
- iOS: Swift, SwiftUI, Swift Concurrency, MVVM, URLSession, XCTest.
- Android: Kotlin, Jetpack Compose, Coroutines/Flow, MVVM, Navigation Compose, Hilt, Retrofit, JUnit.
- CI: GitHub Actions.

## Segurança

- Chaves criptografadas (AES-GCM) antes de persistir.
- Nunca em logs, analytics ou push.
- Revelação protegida por autenticação e autorização no backend.
- O presenteador não acessa a chave de novo após a entrega.

## Modelo de dados (candidatas, a refinar)

User, UserProfile, Game, WishlistItem, Gift (inclui a fase de reserva), GiftSecret, Notification, Report, UserBlock, AuditLog.

## Decisões em aberto

- Login: e-mail/senha com JWT primeiro; Steam OpenID como extra.
- Fonte do catálogo de jogos (seed local por enquanto).
- Critério e responsável pela contestação de presentes.
