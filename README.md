# Gerenciador de Tarefas

Lista de tarefas simples em **um único arquivo HTML** — sem build, sem dependências,
sem backend. As tarefas ficam salvas no `localStorage` do navegador.

## Funcionalidades

- Adicionar tarefa (botão ou <kbd>Enter</kbd>)
- Marcar como concluída / desmarcar
- Contador de pendentes ("2 de 3 pendentes")
- Remover tarefa com desfazer: a removida vai para **Removidas recentemente**,
  de onde volta com um clique — com o estado de concluída que tinha
- Persistência automática entre sessões
- Acessível por teclado e leitor de tela

## Como rodar

Basta abrir o `index.html` no navegador:

```bash
xdg-open index.html      # Linux
# ou: open index.html    # macOS
```

Se preferir servir por HTTP (mais próximo do ambiente publicado):

```bash
python3 -m http.server 8000
# acesse http://localhost:8000
```

## Publicando no GitHub Pages

1. Suba o repositório para o GitHub.
2. Em **Settings → Pages**, selecione a branch `main` e a pasta `/ (root)`.
3. Em alguns instantes o site fica disponível em
   `https://<seu-usuario>.github.io/<nome-do-repo>/`.

Como o projeto é estático e autocontido, não é necessário nenhum passo de build.

## Estrutura

```
index.html   # marcação, estilos e lógica — tudo aqui
```

## Notas técnicas

- O texto das tarefas é inserido via `textContent`, nunca via `innerHTML`,
  para que conteúdo digitado não seja interpretado como HTML.
- Leitura e escrita no `localStorage` são protegidas por `try/catch`: dados
  corrompidos ou armazenamento bloqueado (modo privado) não quebram a aplicação.
- O DOM é a fonte da verdade do estado, e cada alteração reserializa a lista
  inteira. Funciona bem nesta escala; caso a aplicação ganhe edição, filtros
  ou reordenação, vale migrar para um array de estado com uma função
  `render()`.
- Abas abertas em paralelo se mantêm em sincronia pelo evento `storage`:
  quando uma aba grava, as outras recarregam a lista antes que a próxima
  escrita local sobrescreva as alterações vindas de fora.
- Se o navegador bloquear o armazenamento, a aplicação continua utilizável e
  exibe um aviso informando que as alterações não serão preservadas.
- A lixeira é uma rede de segurança para o clique errado, não um histórico:
  guarda no máximo as 10 remoções mais recentes (`TRASH_LIMIT`), em chave
  própria (`tasks-trash`), e as mais antigas saem conforme entram novas.
- As duas listas são gravadas como uma operação só: se a segunda escrita
  falhar, a primeira é revertida. Sem isso, uma remoção feita com o
  armazenamento cheio poderia sair da lista sem entrar na lixeira.
- Esvaziar a lixeira é o único passo sem volta e por isso pede confirmação.
  Ela é feita na própria página, e não com `confirm()`: o diálogo nativo
  bloqueia a thread e não é estilizável. O foco vai para "Cancelar" e
  <kbd>Esc</kbd> desfaz, de modo que a ação destrutiva exige um passo
  deliberado.

## Licença

MIT — veja [LICENSE](LICENSE).
