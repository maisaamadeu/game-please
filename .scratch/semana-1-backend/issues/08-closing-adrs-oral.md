# 08: Fechamento: ADRs e sessão oral

**What to build:** a semana só fecha com evidência de entendimento, não só de código funcionando: quatro ADRs escritos por você, a verificação final do "pronto" e a sessão oral de perguntas sem consulta.

**Blocked by:** 06: Contrato OpenAPI versionado, 07: Exercícios de Java (playground).

**Status:** ready-for-human

Branch sugerida: `w1/08-closing`.

- [ ] ADR: soft delete + índice único parcial (alternativas consideradas e por que o índice comum não serve)
- [ ] ADR: sem pré-checagem de duplicidade; o banco é a fonte da verdade (race condition do `exists` + `insert`)
- [ ] ADR: OpenAPI code-first com `openapi.json` versionado e teste de divergência
- [ ] ADR: `bigint` no catálogo vs `UUID` nos recursos do usuário
- [ ] Cada ADR é escrito por você e revisado pela IA quanto a "a justificativa se sustenta?"
- [ ] Em clone limpo: `docker compose up -d` + `./mvnw test` passam sem passo manual
- [ ] Os testes de N+1, de concorrência (20 execuções seguidas) e de divergência do `openapi.json` estão verdes
- [ ] Sessão oral no chat, sem consulta, com as cinco perguntas: inversão de controle; ciclo de vida de uma requisição no Spring; DTO em vez de expor a entidade; N+1 e como evitar; o que a migration garante
- [ ] A IA aprofunda cada resposta; lacunas viram anotação para revisar antes da semana 4 (registrar abaixo, em Comments)
- [ ] Branches das fatias mergeadas em `main` (local, sem PR)

## Comments
