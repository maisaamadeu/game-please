# 06: Contrato OpenAPI versionado

**What to build:** o contrato da API publicado em OpenAPI, gerado do código e versionado no repositório, com um teste que impede o contrato de divergir do que a API realmente faz. É o que as semanas 2 e 3 (iOS e Android) vão consumir.

**Blocked by:** 04: Listar a wishlist e provar o N+1, 05: Consultar, editar e remover desejo.

**Status:** ready-for-human

Branch sugerida: `w1/06-openapi`.

- [ ] springdoc gera o spec a partir dos controllers e DTOs (code-first)
- [ ] O `openapi.json` gerado é commitado no repositório
- [ ] Um teste gera o spec em memória e **falha** se ele diferir do arquivo versionado; a mensagem de falha diz como regenerar
- [ ] Todos os endpoints (`/games`, `/wishlist` e filhos) estão documentados, com os status possíveis e o formato `ProblemDetail`
- [ ] O header `X-User-Id` aparece no contrato como parâmetro, marcado como temporário
- [ ] Exemplos de requisição/resposta cobrem criação, lista paginada de jogos, erro de validação e 409
- [ ] Você consegue explicar a diferença entre code-first e spec-first e por que a semana usa code-first (insumo para o ADR)

## Comments
