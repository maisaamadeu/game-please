# 07: Exercícios de Java (playground)

**What to build:** três ou quatro exercícios curtos, em testes JUnit isolados do código de produção, para o que o domínio da semana não exercita sozinho. Roda em paralelo às fatias, de preferência nos primeiros dias.

**Blocked by:** None (can start immediately). Depois da fatia 01, os exercícios podem usar o JUnit já configurado.

**Status:** ready-for-human

Não é fatia vertical: é treino de linguagem, fora do código de produção.

- [ ] Exercício de `equals`/`hashCode`: provar o que quebra ao colocar um objeto com `equals` incorreto em um `HashSet`/`HashMap`, e como o `record` resolve
- [ ] Exercício de generics: um tipo ou método genérico próprio (por exemplo, um `Result<T>`), com bound e wildcard, e o que apagamento de tipos (type erasure) impede
- [ ] Exercício de coleções imutáveis e imutabilidade: `List.of`, cópia defensiva, e o que acontece ao tentar mutar
- [ ] Exercício de streams e `Optional`: transformar uma lista com `map`/`filter`/`collect` e tratar ausência sem `null`
- [ ] Cada exercício tem um teste que descreve em uma frase o que foi aprendido e o paralelo com Dart
- [ ] O que aparece de forma natural no projeto (records nos DTOs, `Optional` na consulta de jogo, streams no mapeamento) é anotado como "já exercitado" nas fatias 02 a 05, e não repetido aqui

## Comments
