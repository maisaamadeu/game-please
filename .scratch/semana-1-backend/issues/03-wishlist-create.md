# 03: Adicionar desejo (Regra 1 no banco)

**What to build:** o jogador adiciona um jogo à sua wishlist, e o sistema impede duas vezes o mesmo desejo ativo. A garantia vem do índice único parcial do banco, não de checagem na aplicação, e fica provada por um teste concorrente. É a fatia mais densa da semana: introduz usuário, wishlist, o tratamento central de erros e a conversa entre módulos.

**Blocked by:** 02: Catálogo de jogos e busca.

**Status:** ready-for-human

Branch sugerida: `w1/03-wishlist-create`. Decidir e registrar aqui: tamanho máximo de `reason`; valor padrão de `visibility`; comportamento do header `X-User-Id` ausente, malformado ou de usuário inexistente; biblioteca de UUIDv7 (ou cair para v4).

- [ ] Migration cria `users` (id UUID, nome) e semeia alguns usuários de teste, separada do seed de jogos
- [ ] Migration cria `wishlist_items` (id UUID, usuário, jogo, `status` ACTIVE/REMOVED, `visibility`, `reason`, `created_at`, `updated_at`) e o índice único parcial sobre (usuário, jogo) onde `status = ACTIVE`
- [ ] Auditing preenche `created_at`/`updated_at` como `Instant`/`timestamptz`
- [ ] `POST /wishlist` (body: `gameId`, `visibility`, `reason` opcional) cria um desejo ACTIVE para o usuário do header e responde 201 com o recurso criado
- [ ] Jogo inexistente no catálogo é rejeitado (via API pública do módulo `game`, nunca pelo repository dele)
- [ ] Validação por Bean Validation (`reason` com limite, campos obrigatórios); resposta de erro traz lista `errors` com `field` e `message`, sem ecoar o valor recebido
- [ ] Um tratador central devolve `ProblemDetail` com `type` URI estável, `title`, `status`, `detail`
- [ ] **Sem `exists` antes do insert**: o service insere, captura a violação, confirma pelo nome da constraint que é a do índice parcial e lança exceção de domínio mapeada para 409 com `type` próprio
- [ ] Teste de repository (`@DataJpaTest`, Postgres real): o índice impede dois ACTIVE iguais e **permite** readicionar após `REMOVED`
- [ ] Teste de integração concorrente: duas inserções simultâneas do mesmo (usuário, jogo); exatamente uma vence, a outra recebe 409; estável em 20 execuções seguidas
- [ ] Testes HTTP cobrem 201, 400 de validação, 404 de jogo inexistente e 409 de duplicidade

## Comments
