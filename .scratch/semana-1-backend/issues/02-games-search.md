# 02: Catálogo de jogos e busca

**What to build:** o jogador busca jogos por nome em um catálogo populado por seed e recebe resultados paginados. Primeira fatia que atravessa banco, módulo, API e testes.

**Blocked by:** 01: Esqueleto verificável do zero.

**Status:** ready-for-human

Branch sugerida: `w1/02-games-search`. Decidir aqui: termo de busca vazio devolve o catálogo todo paginado?

- [ ] Migration cria `games` (id `bigint identity`, `steam_app_id` único, nome, ano de lançamento, gêneros em texto simples, URL da capa)
- [ ] Migration de seed com ~60 jogos reais (nome e `appid` da Steam); o SQL é gerado por um script pequeno escrito por você
- [ ] `GET /games?query=&page=&size=` filtra por nome com `ILIKE %query%`, ordena por nome e pagina por offset
- [ ] A resposta usa envelope próprio (`items`, `page`, `size`, `totalElements`), não o `Page<>` do Spring serializado direto
- [ ] DTO de resposta é um `record`; a entidade não é exposta
- [ ] Busca sem resultado devolve lista vazia com 200
- [ ] Parâmetros inválidos (`size` negativo ou acima do limite, `page` negativo) devolvem erro de validação
- [ ] O módulo `game` expõe uma API pública mínima (consulta de jogo por id, retornando `Optional`) para o `wishlist` usar na fatia 03
- [ ] Testes de integração pela API HTTP cobrem: busca parcial, ignorar caixa, paginação, ordem, resultado vazio
- [ ] Se houver normalização do termo de busca, ela tem teste unitário puro

## Comments
