# GamePlease

Plataforma social de wishlist e presentes de jogos da Steam: jogadores publicam jogos que gostariam de ganhar, e outras pessoas podem realizar o desejo. Sem loja e sem pagamentos.

## Language

**Player**:
Pessoa com conta na plataforma. Dona de uma wishlist.
_Avoid_: User, usuário, cliente

**Game**:
Jogo do catálogo, identificado também pelo seu `appid` da Steam. É o que se deseja; não pertence a nenhum player.
_Avoid_: Título, produto, item

**Wishlist item**:
O desejo de um player por um game, com visibilidade e motivo opcional. Seu estado é independente do de qualquer presente.
_Avoid_: Desejo solto, wish, entry

**Active wishlist item**:
Wishlist item que o player mantém hoje. Um player não pode ter dois para o mesmo game.
_Avoid_: Desejo vigente, wishlist item "aberto"

**Removed wishlist item**:
Wishlist item que o player retirou da sua lista. Deixa de contar para a regra de duplicidade, então o mesmo game pode ser desejado de novo.
_Avoid_: Deleted, apagado

**Visibility**:
Se um wishlist item é público (visível a outros players) ou privado (só do dono).
_Avoid_: Privacidade, scope

**Reason**:
Texto opcional em que o player explica por que deseja o game.
_Avoid_: Comentário, nota, descrição
