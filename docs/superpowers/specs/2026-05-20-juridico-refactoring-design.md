# Design: Refatoração da Seção Jurídico

**Data:** 2026-05-20  
**Objetivo:** Consolidar a seção "Desclassificar" (renomeada para "Jurídico") com padronização visual, sincronização de links, correção de bugs e melhorias de UX.

---

## 1. Escopo

### Alterações Funcionais
1. **Sistema centralizado de links** — adicionar campo de link em cada item do Checklist, sincronizar com Acompanhamento
2. **Padronização visual** — Recursos e Respostas seguem o mesmo padrão HTML/CSS do Checklist (grupos colapsáveis + itens em lista)
3. **Correção de bugs:**
   - Toggle "selecionar todos" em Acompanhamento não desmarcava
   - Aba superior (CHECKLIST/RECURSOS/RESPOSTAS) desaparecia ao expandir itens
4. **Renomeação** — "Desclassificar" → "Jurídico" (nav, títulos, eyebrows, seções)

---

## 2. Arquitetura de Dados

### 2.1 Objeto Centralizado de Links

```javascript
let checklistLinks = {}; // { "0-0": "https://...", "0-1": "https://..." }
```

Persiste em `localStorage` com key `checklist_links`.

### 2.2 Funções Helper

```javascript
function saveChecklistLink(gi, ii, url) {
  const key = `${gi}-${ii}`;
  if (!url || !url.trim()) {
    delete checklistLinks[key];
  } else {
    checklistLinks[key] = url.trim();
  }
  localStorage.setItem('checklist_links', JSON.stringify(checklistLinks));
  renderJuridico(); // re-render para atualizar UI
}

function getChecklistLink(gi, ii) {
  return checklistLinks[`${gi}-${ii}`] || '';
}

function loadChecklistLinks() {
  try {
    checklistLinks = JSON.parse(localStorage.getItem('checklist_links')) || {};
  } catch {
    checklistLinks = {};
  }
}

function syncLinksToAcompanhamento(licId) {
  // Quando abre um item em Acompanhamento, carrega os links do Checklist
  // Este passo é informativo — os links aparecem em preview/referência
}
```

---

## 3. Estrutura HTML Padronizada

Todas as 3 seções (Checklist, Recursos, Respostas) renderizam com a mesma estrutura base:

```html
<div class="grupo" data-grupo="INDEX">
  <div class="grupo-header" onclick="toggleGrupo(INDEX)">
    <span class="grupo-eyebrow">TÍTULO DA SEÇÃO</span>
    <span class="grupo-count">N itens</span>
    <span class="grupo-chevron">▼</span>
  </div>
  <div class="grupo-items">
    <div class="ck-item">
      <div class="ck-item-header" onclick="toggleItem('KEY', event)">
        <span class="ck-item-titulo">Nome do Item</span>
        <span class="ck-item-badge">TAG</span>
        <span class="ck-item-arrow">▼</span>
      </div>
      <div class="ck-expand">
        <!-- Conteúdo específico da seção -->
        <!-- Botões de ação (link, editar, copiar) -->
      </div>
    </div>
  </div>
</div>
```

---

## 4. Mudanças por Seção

### 4.1 Checklist
- Manter estrutura existente
- **Novo:** Campo de link inline ao expandir cada item (ícone 🔗 + input oculto)
- Sincronizar com `saveChecklistLink(gi, ii, url)`

### 4.2 Recursos
- **Antes:** Blocos com accordion aninhado (grupos → itens)
- **Depois:** Mesma estrutura do Checklist (grupos colapsáveis, itens em lista)
- Ações: Editar, Copiar (sem links, pois já há no Checklist)

### 4.3 Respostas
- **Antes:** Blocos com accordion aninhado
- **Depois:** Mesma estrutura do Checklist
- Ações: Editar, Copiar (sem links)

---

## 5. Correções de Bugs

### 5.1 Toggle "Selecionar Todos" em Acompanhamento

**Problema:** Ao clicar o checkbox "selecionar todos", marca todos. Ao clicar novamente, não desmarca.

**Causa:** Lógica em `acompToggleSelectAll()` não verifica estado anterior.

**Solução:**
```javascript
function acompToggleSelectAll(checked) {
  if (checked) {
    // Marcar todos
    const table = document.querySelector('.acomp-table tbody');
    table?.querySelectorAll('input[type="checkbox"]:not(#acomp-check-all)').forEach(cb => {
      cb.checked = true;
      const id = cb.dataset.id;
      if (id) acompSelectedIds.add(id);
    });
  } else {
    // Desmarcar todos
    acompSelectedIds.clear();
    document.querySelectorAll('.acomp-table input[type="checkbox"]').forEach(cb => {
      cb.checked = false;
    });
  }
  acompRenderTable();
}
```

### 5.2 Sticky Tabs (Aba Superior Visível)

**Problema:** Ao expandir item, a aba `.ref-tabs` some.

**Solução:** CSS sticky positioning

```css
.ref-tabs {
  position: sticky;
  top: 0;
  z-index: 15;
  background: var(--bg);
  padding: 12px 0;
  border-bottom: 1px solid var(--line);
}
```

---

## 6. Renomeação "Desclassificar" → "Jurídico"

Alterar em:
- ✅ Botão nav: `data-section="desclassificar"` → `data-section="juridico"`
- ✅ Texto do botão: "Desclassificar" → "Jurídico"
- ✅ Seções eyebrow: "Desclassificar" → "Jurídico"
- ✅ Títulos: "Checklist de Desclassificação" → "Checklist Jurídico"
- ✅ Variável global: `desclassificarTab` → `juridicoTab`
- ✅ Função: `renderDesclassificar()` → `renderJuridico()`
- ✅ Função: `switchDesclassificarTab()` → `switchJuridicoTab()`

---

## 7. Fluxo de Sincronização de Links

1. Usuário clica ícone 🔗 em um item do Checklist
2. Abre input oculto para colar URL
3. Ao clicar "Salvar" ou sair do campo, chama `saveChecklistLink(gi, ii, url)`
4. Link fica armazenado em `checklistLinks` + localStorage
5. Quando abre Acompanhamento e seleciona documentos, o sistema pode referenciar esses links (exibir como sugestão/preview)

---

## 8. Testing

- [ ] Expandir itens do Checklist — campo de link aparece e funciona
- [ ] Clicar "selecionar todos" → marca todos
- [ ] Clicar novamente → desmarca todos
- [ ] Aba superior permanece visível ao expandir
- [ ] Recursos e Respostas com layout de lista (não blocos)
- [ ] Renomeação consistente em toda a UI

---

## 9. Implementação

Trabalhar em paralelo:
1. **Refactor de dados:** checklistLinks + helpers
2. **Refactor de HTML:** padronizar Recursos + Respostas
3. **CSS:** sticky tabs + ajustes de spacing
4. **Correções:** toggle selecionar todos + links no Checklist
5. **Renomeação:** find & replace global

---

**Status:** ✅ Design aprovado — pronto para implementação
