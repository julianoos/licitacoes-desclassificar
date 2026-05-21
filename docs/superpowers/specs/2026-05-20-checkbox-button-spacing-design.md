# Design: Melhorar Espaçamento e Tamanho de Checkboxes e Botões

**Data:** 2026-05-20  
**Escopo:** Ajustes visuais de layout na tabela de acompanhamento de licitações

## Context

A tabela de licitações tem espaço vazio desbalanceado à direita, e os botões de ação são menores que o botão LINK. O checkbox também está muito próximo à borda esquerda.

## Objetivo

Melhorar a distribuição visual de espaço e padronizar tamanhos dos componentes de ação.

## Mudanças Propostas

### 1. Checkbox — Melhor Distribuição Horizontal

**Arquivo:** `index.html` linha 469 (CSS `.acomp-select-check`)

**Mudança:**
- Padding atual: não explícito (valor padrão)
- Padding novo: `4px 10px` (top/bottom 4px, left/right 10px)
- Aumenta padding esquerdo de ~2px para 10px, move checkbox mais para a direita

**Locais afetados:**
- Célula do checkbox no cabeçalho (linha 1265)
- Célula do checkbox em cada linha (linha 975)

### 2. Botões de Ação — Aumentar Tamanho e Espaçamento

**Arquivo:** `index.html`

**Mudança 1 — `.acomp-btn-icon` (linha 524-529):**
- Width: `22px` → `28px`
- Height: `22px` → `28px`
- Font-size: `9px` → `13px`

**Mudança 2 — `.acomp-actions-cell` (linha 536):**
- Gap: `4px` → `8px`

**Mudança 3 — SVG dos ícones:**
- Aumentar viewBox/dimensões de `12×12` para `13×13`
- Ícones afetados: eye (linha 990), lock (linha 991), edit (linha 992), delete (linha 993)

## Padrão Estabelecido

Após as mudanças, todos os botões de ação (eye, lock, edit, delete, globe/LINK) terão:
- Dimensão: `28×28px`
- Ícone interno: `13×13px`
- Font-size container: `13px`
- Espaçamento entre botões: `8px`

## Verificação

1. Abrir `index.html` no navegador
2. Verificar que checkbox está mais afastado da borda esquerda
3. Verificar que botões (eye, lock, edit, delete) têm mesmo tamanho do botão LINK (globe)
4. Verificar espaçamento entre botões foi aumentado
5. Confirmar que tabela mantém proporções e está bem distribuída

## Notas Técnicas

- Nenhuma mudança em HTML structure, apenas CSS e SVG dimensions
- Checkbox cell width atual: `14px` (pode manter ou ajustar conforme necessário)
- Todos os valores em pixels (px) para consistência
