# 04: Listar a wishlist e provar o N+1

**What to build:** o jogador vê a sua wishlist com o nome do jogo em cada item, só com os desejos ativos. Primeiro você escreve a versão ingênua, prova o N+1 por teste e depois o corrige, mantendo o teste como regressão. O objetivo é o N+1 ter sido **visto**, não só lido.

**Blocked by:** 03: Adicionar desejo (Regra 1 no banco).

**Status:** ready-for-human

Branch sugerida: `w1/04-wishlist-list`. Ordem obrigatória: ingênuo, teste vermelho, correção, teste verde.

- [ ] `GET /wishlist` devolve apenas os desejos ACTIVE do usuário do header, cada um com o nome do jogo
- [ ] A associação com `Game` é lazy; a versão ingênua é implementada primeiro
- [ ] Um teste conta as queries emitidas (Hibernate Statistics) numa lista com vários itens e **falha** na versão ingênua, mostrando 1 + N queries
- [ ] A correção usa entity graph ou join fetch e o mesmo teste passa, com número de queries constante, independente de N
- [ ] A resposta usa DTO (`record`); a entidade não é serializada
- [ ] Itens REMOVED e itens de outros usuários não aparecem
- [ ] Lista vazia devolve 200 com lista vazia
- [ ] Você consegue explicar por que o `open-in-view` desligado ajuda a expor o problema em vez de escondê-lo

## Comments
