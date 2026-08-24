# Validação — MW Scene Button Card + Element (23/08/2026)

Os dois nasceram, estão **no HA** e esperam seu olho. Nada de DevOps, GitHub ou
harness ainda — isso é o passo depois do seu «ok».

## Onde olhar

**Sidebar → 🎬 SCENE BUTTON LAB** (`/mw-scene-button-lab`, só administrador).
Se a tela vier crua, ⌘⇧R uma vez — recurso novo do Lovelace pede recarga dura.

| view | o que tem |
|---|---|
| **BOTÃO** | 4 fileiras: (1) seu YAML de hoje traduzido, com `conditional`; (2) o mesmo **sem** `conditional`, com `state_entity`; (3) o ar-condicionado; (4) casos de borda — confirmação em papel escuro, só-ícone, sem pulso, e uma **cena que não existe de propósito** |
| **PLANTA** | 3 botões do elemento sobre a `PLANTA-53.2.png`, no escritório |

## O que eu preciso que você confira

1. **A lista de cenas** — no editor de qualquer botão do lab, campo «Cena
   (área · nome)»: área no cabeçalho, cena identada embaixo, filtro ao lado.
   A ordem das áreas tem que ser 🟨 🟧 ⬛️ 🟦 🟩 🟪 ⬜️ (🟫 não existe) 🟥 🏠 🗄️,
   depois as demais em ordem alfabética.
2. **O `<select>` não pode fechar sozinho** enquanto você escolhe (foi o bug do
   Rainbow — aqui a lista só repinta quando o registro muda e ninguém está com
   o campo em foco).
3. **A cor do ícone** sem `card_mod`: verde nos de ajuste, vermelho no DESLIG.
4. **O pulso de papel** no toque — é o único retorno que uma cena dá.
5. **A 2ª fileira**: com a tomada do climatizador ligada, o papel tem que estar
   **aceso**; desligada, apagado.
6. **A planta**: tamanho e posição dos três botões, e se o texto encolhe junto
   com o botão ao redimensionar a janela.
7. **Segurar 500 ms** abre o more-info da *tomada* (não o da cena, que só
   mostra carimbo de tempo) — diga se prefere o contrário.

## Decisão que tomei sozinho (e dá para mudar num minuto)

**Ordem das cenas dentro da área:** alfabética pelo **nome inteiro**, emoji
incluído. Como os nomes começam por emoji, isso agrupa por família (❄️ ar,
💨 climatizador, 🪟 janela) em vez de ordenar pelas palavras. Se você quiser
alfabética **pelas palavras**, é uma linha na chave de ordenação.

## O que ficou verificado — e o que não

| verificado | como |
|---|---|
| sintaxe dos dois arquivos | `node --check` |
| 21 checagens de comportamento (ordem das áreas, filtro, U+FE0F, desenho, `state_entity`, cena morta) | `node tools/probe.js` nos dois repos |
| blocos compartilhados iguais à fonte canônica | `IA/tools/check-embeds.sh` |
| **entrega no destino** | `curl` em `/hacsfiles/…` devolvendo 200 e o marcador novo |
| recursos do Lovelace registrados | WebSocket `lovelace/resources` |
| dashboard do lab criado com as 2 views | WebSocket `lovelace/config` |

**Não verificado:** a tela. Este Mac não abre o frontend do HA autenticado, e
por isso nada aqui prova que o botão *parece* certo — só que ele chegou e que
o código faz o que diz. A conferência visual é sua.

## Depois do seu «ok»

1. `gh repo create` dos dois (públicos, como os outros 19 componentes);
2. DevOps padrão via `IA/tools/mw-devops.sh` (CI, auto-release, probe, regras);
3. marca «Visão de Empresa» — os dois já estão como `pendente` no `repos.tsv`;
4. harness: knowledge do picker, ADR se valer, skill do repo, `make check`,
   CHANGELOG, memória.
