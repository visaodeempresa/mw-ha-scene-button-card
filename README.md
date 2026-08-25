<!-- MW-BRAND:BEGIN — gerado por IA/tools/mw-brand.sh · não editar à mão -->
<p align="center">
  <a href="https://github.com/visaodeempresa">
    <img src="https://mayconsoftware.github.io/assets/ve/LOGO_VISAO_DE_EMPRESA_HEIGHT-64px.png" alt="Visão de Empresa — MAYCON WILLIAN OLIVEIRA" height="64">
  </a>
  <br>
  <sub><b>Visão de Empresa</b> · componente de Home Assistant por MAYCON WILLIAN OLIVEIRA</sub>
</p>
<!-- MW-BRAND:END -->

# MW Scene Button Card

`custom:mw-scene-button-card` — o **botão de papel que dispara uma cena**, com
a lista de cenas **agrupada por área** no editor.

```yaml
type: custom:mw-scene-button-card
entity: scene.ligar_climatizador_escritorio
name: CLIMAT
icon: mdi:play
color_icon: green
state_entity: switch.tomada_climatizador_do_escritorio_socket_1
```

Irmão do [MW Simple Button Card](https://github.com/visaodeempresa/mw-ha-simple-button-card):
mesma geometria, mesmo papel, mesma confirmação. O que muda é que aqui a
entidade é uma **cena** — e cena não é interruptor.

## Por que um card só para cena

`scene.*` **nunca vale `on`**: o estado de uma cena é o carimbo de tempo da
última vez que ela rodou. Num card que acende por estado, a barra inteira de
botões de cena fica afundada e escura, o ícone de "ligado" nunca aparece, e a
cor do ícone apagado é fixa — era por isso que cada botão precisava de um
`card_mod` só para pintar o ícone de verde ou vermelho.

Aqui isso é o comportamento normal, não um defeito a contornar:

| o problema | como este card resolve |
|---|---|
| a barra inteira nasce afundada e escura | **o botão de cena nasce aceso** — cena é ação, não interruptor |
| ícone de ligado que nunca aparece | **um** ícone (`icon`), sempre visível |
| ícone apagado é branco fixo | `color_icon` no editor — sem `card_mod` |
| cena não devolve estado nenhum | **aperto do papel** (`flash`) ao ativar: o dedo sabe que valeu |
| o botão devia refletir o aparelho | `state_entity` opcional — aí a tomada desligada apaga o papel |
| achar a cena certa entre 70+ | lista **agrupada por área**, com filtro |

### Quando o papel apaga

Só em duas situações, e as duas são de verdade:

1. a cena está **indisponível** ou foi **desabilitada** — vem o ícone de
   indisponível, com o rótulo riscado;
2. você deu um **`state_entity`** e ele está desligado.

## Fileira: N botões num card só

Quatro botões dentro de um `type: grid` do HA **nunca** ficam com o mesmo
espaço entre si que os botões do [MW Humidifier](https://github.com/visaodeempresa/mw-ha-humidifier-card),
e o culpado não é o `gap`: com `square: true` a grade do HA usa
`grid-auto-rows: 1fr`, e da segunda linha em diante a linha estica para
preencher a altura sobrada da coluna.

Por isso o card também sabe **ser** a grade:

```yaml
type: custom:mw-scene-button-card
button_columns: 2
gap: 8                 # px, o mesmo do MW Humidifier
hide_label: true
icon_size: 46%
buttons:
  - entity: scene.ligar_climatizador_escritorio
    icon: mdi:play
    color_icon: rgba(0, 100, 0, 0.8)
    paper_color: green-6
    show_when: { entity: switch.tomada_climatizador, state: "off" }
  - entity: scene.desligar_climatizador_escritorio
    icon: mdi:stop
    color_icon: red
    paper_color: red-6
    show_when: { entity: switch.tomada_climatizador, state: "on" }
```

O que está no card vale para todos os botões; o que está no item vence.
`show_when: {entity, state}` substitui o `type: conditional` por botão — e
`state` aceita uma lista.

**A fileira se edita pelo YAML.** O editor visual mexe em um botão só e
apagaria a lista, então ele se recusa e explica.

## A lista de cenas

O seletor de entidade do HA mostra cena por nome, tudo misturado. Aqui o campo
é um `<select>` com `<optgroup>`: **área no cabeçalho, cena identada embaixo**,
com uma caixa de filtro ao lado.

A ordem das áreas é a da casa, não a alfabética:

```
🟨  🟧  ⬛️  🟦  🟩  🟪  ⬜️  🟫  🟥  🏠  🗄️   →  depois as demais, em ordem
                                                 alfabética, e por último as
                                                 cenas sem área
```

Área com prefixo composto (`🟥🚪 HALL DE ACESSO`) entra no bloco do primeiro
emoji. Dentro de cada área as cenas ficam em **ordem alfabética pelo nome
inteiro** — como o nome costuma começar por emoji, as famílias (❄️ ar,
💨 climatizador, 🪟 janela) acabam agrupadas.

## Opções

| chave | padrão | o que faz |
|---|---|---|
| `entity` | — | **a cena** (`scene.*`). Obrigatória |
| `name` | friendly_name | o rótulo do botão |
| `icon` | `mdi:play` | o ícone — num botão de cena é este que aparece **sempre** |
| `state_entity` | — | entidade que dá estado ao botão (a tomada, a luz…) |
| `state_on` | `on` | valor de `state_entity` que acende o papel |
| `icon_off` | — | ícone quando `state_entity` está **desligado** |
| `flash` | `true` | aperto do papel ao ativar a cena |
| `transition` | — | segundos de transição passados ao `scene.turn_on` |
| `color_icon` / `color_icon_off` | — | a cor do ícone (a segunda só com `state_entity`) |
| `paper_color` | `paper` | 1 dos 49 papéis encardidos (7 matizes × 7 tons) |
| `confirm` · `confirm_3d` · `confirm_paper_*` | `false` | balão de confirmação em papel |
| `hide_label` · `name_position` · `icon_size` · `name_size` · `name_gap` | — | geometria |
| `control` · `haptic` · `animate` · `icon_shadow` | — | comportamento |

Cores de estado (`color_on_name`, `color_off_name`, `color_off_bg`,
`color_on_border`, `color_off_border`, `color_unavail`) ficam na gaveta
**Cores** do editor, com cor + transparência.

## Toque

- **tap** — `scene.turn_on` na cena, vibração e aperto do papel;
- **hold (500 ms)** — `more-info` do `state_entity` quando existe, senão da
  própria cena (a caixa da cena só mostra o carimbo de tempo).

## Na planta baixa

O mesmo botão para `picture-elements` mora em
[MW Scene Button Element](https://github.com/visaodeempresa/mw-ha-scene-button-element).

## Instalação

HACS → Repositórios personalizados → `visaodeempresa/mw-ha-scene-button-card`,
categoria **Dashboard**.

## Verificação

```bash
node --check dist/mw-scene-button-card.js
node tools/probe.js
```

---

MIT © MAYCON WILLIAN OLIVEIRA
