# 01: Esqueleto verificável do zero

**What to build:** um projeto Spring Boot que sobe, conversa com um PostgreSQL de verdade e prova isso por teste, em um clone limpo e sem passo manual. É a fatia que prova o "do zero" cedo, antes de existir qualquer regra de negócio.

**Blocked by:** None (can start immediately).

**Status:** ready-for-human

Branch sugerida: `w1/01-skeleton`. Pré-requisitos de ambiente (JDK 21 via SDKMAN e runtime Docker) fazem parte desta fatia.

- [ ] JDK 21 (Temurin) instalado via SDKMAN e fixado em arquivo versionado; `./mvnw -v` usa Java 21
- [ ] Runtime Docker instalado e funcionando (`docker info` responde)
- [ ] Projeto Spring Boot (última GA estável) gerado com Maven wrapper, pacote base definido e estrutura em módulos (`game`, `wishlist`) esboçada
- [ ] `docker compose up -d` sobe o PostgreSQL com credenciais de desenvolvimento fixas; o container fica saudável
- [ ] A aplicação conecta no banco do Compose por padrão, sem `.env` nem variável obrigatória
- [ ] Flyway configurado e executando na subida; Hibernate só valida o schema; `open-in-view` desligado
- [ ] Um teste de integração com Testcontainers (`@ServiceConnection`, container compartilhado) sobe o contexto e confirma que o Flyway rodou
- [ ] O `backend/README.md` descreve como rodar em três comandos
- [ ] Em clone limpo: `docker compose up -d` + `./mvnw test` passam sem nenhum passo manual

## Comments
