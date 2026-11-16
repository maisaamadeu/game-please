# 05: Consultar, editar e remover desejo

**What to build:** o jogador abre o detalhe de um desejo, muda o motivo ou a visibilidade e remove o desejo (soft delete). Recurso de outra pessoa responde 404, como se não existisse.

**Blocked by:** 03: Adicionar desejo (Regra 1 no banco).

**Status:** ready-for-human

Branch sugerida: `w1/05-wishlist-edit-remove`. Pode andar em paralelo com a 04, mas as duas mexem no mesmo módulo; prefira terminar uma antes de começar a outra.

- [ ] `GET /wishlist/{id}` devolve o desejo do usuário do header
- [ ] `PATCH /wishlist/{id}` altera só `reason` e `visibility`; qualquer tentativa de trocar o jogo é rejeitada e documentada
- [ ] `DELETE /wishlist/{id}` marca o desejo como REMOVED (a linha permanece) e responde 204
- [ ] Remover um desejo já REMOVED, ou consultar/editar um REMOVED, segue uma regra única e documentada (sugestão: 404)
- [ ] Desejo de outro usuário responde 404 em GET, PATCH e DELETE, sem vazar existência
- [ ] Depois de remover, é possível adicionar o mesmo jogo de novo (nova linha), e o teste de integração prova esse fluxo ponta a ponta
- [ ] Validação do PATCH (limite de `reason`) reaproveita o padrão de erro da fatia 03
- [ ] Testes HTTP cobrem: sucesso, 404 alheio, 404 inexistente, remoção seguida de readição, e `updated_at` mudando no PATCH

## Comments
