# Refatoração da Seção Jurídico — Plano de Implementação

> **Para agentic workers:** Use superpowers:subagent-driven-development (recomendado) ou superpowers:executing-plans para executar este plano task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Objetivo:** Consolidar a seção "Jurídico" (renomeada de "Desclassificar") com padronização visual, sincronização de links, correção de bugs e melhoria de sticky tabs.

**Arquitetura:** Refatoração do arquivo `index.html` em 8 tarefas sequenciais: (1) adicionar sistema de links centralizado, (2-3) padronizar Recursos/Respostas com padrão do Checklist, (4) integrar links no Checklist, (5) corrigir toggle, (6) CSS sticky, (7-8) renomear + testar.

**Tech Stack:** HTML/CSS/JavaScript vanilla (single-file app, localStorage)

---

## Estrutura de Arquivos

**Modificações:**
- `index.html` — único arquivo, múltiplas seções:
  - Variáveis globais: `checklistLinks`, `juridicoTab`
  - Funções helpers: `saveChecklistLink()`, `getChecklistLink()`, `loadChecklistLinks()`
  - Refactor: `renderJuridico()`, `renderChecklist()`, `renderRecursos()`, `renderRespostas()`, `switchJuridicoTab()`
  - Correção: `acompToggleSelectAll()`
  - CSS: `.ref-tabs { position: sticky; ... }`

---

## Tarefas

### Task 1: Adicionar Sistema Centralizado de Links

**Arquivos:**
- Modificar: `index.html` (adicionar variáveis globais e helpers, linhas ~856-880)

- [ ] **Step 1: Localizar onde está `desclassificarTab`**

Abra `index.html` e procure a linha com `let desclassificarTab = 'checklist';` (por volta de linha 856).

- [ ] **Step 2: Adicionar novo objeto de links logo após `desclassificarTab`**

```javascript
let desclassificarTab = 'checklist';
let checklistLinks = {}; // { "0-0": "https://...", "0-1": "https://..." }
```

- [ ] **Step 3: Adicionar função `loadChecklistLinks()` (antes de `renderDesclassificar`)**

Procure a linha `function renderDesclassificar(){` (por volta de 2221) e insira antes:

```javascript
function loadChecklistLinks(){
  try {
    checklistLinks = JSON.parse(localStorage.getItem('checklist_links')) || {};
  } catch(e) {
    checklistLinks = {};
  }
}

function saveChecklistLink(gi, ii, url){
  const key = `${gi}-${ii}`;
  if(!url || !url.trim()){
    delete checklistLinks[key];
  } else {
    checklistLinks[key] = url.trim();
  }
  localStorage.setItem('checklist_links', JSON.stringify(checklistLinks));
  renderDesclassificar();
}

function getChecklistLink(gi, ii){
  return checklistLinks[`${gi}-${ii}`] || '';
}
```

- [ ] **Step 4: Chamar `loadChecklistLinks()` na inicialização do app**

Procure a função `initApp()` ou `window.onload` (por volta de linha 740-760) e adicione `loadChecklistLinks();` no início.

Se não houver função de inicialização óbvia, procure onde `acompData` é carregado do localStorage e adicione logo após:

```javascript
loadChecklistLinks();
```

- [ ] **Step 5: Commit**

```bash
cd "D:\projetosclaude\licitacoes-desclassificar"
git add index.html
git commit -m "feat: adicionar sistema centralizado de links para Checklist"
```

---

### Task 2: Padronizar Recursos com Novo Padrão HTML

**Arquivos:**
- Modificar: `index.html` (função `renderRecursos()`, linhas ~2385-2419)

- [ ] **Step 1: Verificar estrutura atual de `renderRecursos()`**

Leia linhas 2385-2419 para entender como estão estruturados os grupos e itens.

- [ ] **Step 2: Reescrever `renderRecursos()` para usar padrão padronizado**

Substitua a função inteira por:

```javascript
function renderRecursos(){
  let html = `<div class="section-header">
    <div class="section-eyebrow"><span class="dot"></span>Jurídico<span class="dot"></span>Textos Prontos</div>
    <h1 class="section-title">Recursos</h1>
    <p class="section-subtitle">Textos prontos para contestar cada tipo de documento. Clique em Copiar para usar.</p>
  </div>
  <div id="rec-grupos">`;
  
  CHECKLIST.forEach((g, gi) => {
    html += `<div class="grupo" id="rec-grupo-${gi}" data-grupo="${gi}">
      <div class="grupo-header" onclick="toggleGrupo('rec-${gi}')">
        <span class="grupo-eyebrow">${escHtml(g.secao)}</span>
        <span class="grupo-count">${g.itens.length} itens</span>
        <span class="grupo-chevron">▼</span>
      </div>
      <div class="grupo-items">`;
    
    g.itens.forEach((item, ii) => {
      const key = `${gi}-${ii}`;
      const savedRecurso = localStorage.getItem('recurso_'+key) || item.recurso;
      const isOpen = !!recursosExpanded[key];
      
      html += `<div class="ck-item" id="rec-item-${key}" data-key="${key}">
        <div class="ck-item-header" onclick="toggleRecursoItem('${key}',event)">
          <span class="ck-item-titulo">${escHtml(item.tipo)}</span>
          <span class="ck-item-badge">RECURSO</span>
          <span class="ck-item-arrow">▼</span>
        </div>
        ${isOpen ? `<div class="ck-expand">
          <div class="ck-recurso-text" id="rec-${key}">${escHtml(savedRecurso)}</div>
          <div class="recursos-item-actions">
            <button class="ck-btn" onclick="editRecursoRec('${key}')">Editar</button>
            <button class="ck-btn" onclick="copyRecursoRec('${key}',this)">Copiar</button>
          </div>
        </div>` : ''}
      </div>`;
    });
    
    html += `</div></div>`;
  });
  
  html += `</div>`;
  document.getElementById('main').innerHTML = html;
}
```

- [ ] **Step 3: Atualizar `toggleRecursoItem()` para aceitar evento**

Procure `function toggleRecursoItem(key)` (por volta de 2321) e modifique para:

```javascript
function toggleRecursoItem(key, e){
  if(e) e.stopPropagation();
  recursosExpanded[key] = !recursosExpanded[key];
  renderRecursos();
}
```

- [ ] **Step 4: Testar Recursos no navegador**

Abra o navegador, clique na aba "RECURSOS", verifique:
- Grupos colapsáveis funcionam
- Itens expandem/contraem individualmente
- Botões "Editar" e "Copiar" funcionam

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "refactor: padronizar Recursos com padrão do Checklist"
```

---

### Task 3: Padronizar Respostas com Novo Padrão HTML

**Arquivos:**
- Modificar: `index.html` (função `renderRespostas()`, linhas ~2420-2460 aprox)

- [ ] **Step 1: Procurar função `renderRespostas()`**

Procure `function renderRespostas(){` no arquivo.

- [ ] **Step 2: Reescrever `renderRespostas()` para usar padrão padronizado**

Substitua a função por:

```javascript
function renderRespostas(){
  let html = `<div class="section-header">
    <div class="section-eyebrow"><span class="dot"></span>Jurídico<span class="dot"></span>Respostas Prontas</div>
    <h1 class="section-title">Respostas</h1>
    <p class="section-subtitle">Textos de resposta a questionamentos. Clique em Copiar para usar.</p>
  </div>
  <div id="resp-grupos">`;
  
  RESPOSTAS.forEach((tipo, ti) => {
    html += `<div class="grupo" id="resp-grupo-${ti}" data-grupo="${ti}">
      <div class="grupo-header" onclick="toggleGrupo('resp-${ti}')">
        <span class="grupo-eyebrow">${escHtml(tipo.tipo)}</span>
        <span class="grupo-count">${tipo.itens.length} itens</span>
        <span class="grupo-chevron">▼</span>
      </div>
      <div class="grupo-items">`;
    
    tipo.itens.forEach((item, ii) => {
      const key = `${ti}-${ii}`;
      const savedResposta = localStorage.getItem('resposta_'+key) || item.texto;
      const isOpen = !!respostasExpanded[key];
      
      html += `<div class="ck-item" id="resp-item-${key}" data-key="${key}">
        <div class="ck-item-header" onclick="toggleRespostaItem('${key}',event)">
          <span class="ck-item-titulo">${escHtml(item.subtipo)}</span>
          <span class="ck-item-badge">RESPOSTA</span>
          <span class="ck-item-arrow">▼</span>
        </div>
        ${isOpen ? `<div class="ck-expand">
          <div class="ck-resposta-text" id="resp-${key}">${escHtml(savedResposta)}</div>
          <div class="resposta-item-actions">
            <button class="ck-btn" onclick="editRespostaItem('${key}')">Editar</button>
            <button class="ck-btn" onclick="copyRespostaItem('${key}',this)">Copiar</button>
          </div>
        </div>` : ''}
      </div>`;
    });
    
    html += `</div></div>`;
  });
  
  html += `</div>`;
  document.getElementById('main').innerHTML = html;
}
```

- [ ] **Step 3: Atualizar `toggleRespostaItem()` para aceitar evento**

Procure `function toggleRespostaItem(key)` (por volta de 2339) e modifique para:

```javascript
function toggleRespostaItem(key, e){
  if(e) e.stopPropagation();
  respostasExpanded[key] = !respostasExpanded[key];
  renderRespostas();
}
```

- [ ] **Step 4: Testar Respostas no navegador**

Abra o navegador, clique na aba "RESPOSTAS", verifique:
- Grupos colapsáveis funcionam
- Itens expandem/contraem individualmente
- Botões "Editar" e "Copiar" funcionam
- Layout é similar a Recursos

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "refactor: padronizar Respostas com padrão do Checklist"
```

---

### Task 4: Adicionar Campo de Link no Checklist

**Arquivos:**
- Modificar: `index.html` (função `renderChecklist()`, linhas ~2243-2289)

- [ ] **Step 1: Localizar função `renderChecklist()`**

Procure `function renderChecklist(){` (por volta de linha 2243).

- [ ] **Step 2: Modificar o HTML do `ck-item` para incluir campo de link**

Dentro de `renderChecklist()`, encontre o trecho que renderiza cada item (por volta de linha 2265):

```javascript
html += `<div class="ck-item" id="ck-${key}" data-tipo="${item.tipo.toLowerCase()}">
  <div class="ck-item-header" onclick="toggleItem('${key}',event)">
    <span class="ck-item-titulo">${item.tipo}</span>
    <span class="ck-item-badge">${g.badge}</span>
    <span class="ck-item-arrow">▼</span>
  </div>
  <div class="ck-expand">
    <div class="ck-panels">
      <div>
        <span class="ck-panel-label">Jurisprudência</span>
        <div class="ck-panel-content">${escHtml(item.jurisprudencia)}</div>
      </div>
      <div>
        <span class="ck-panel-label">Observações</span>
        <div class="ck-panel-content">${obsFormatted}</div>
      </div>
    </div>
  </div>
</div>`;
```

Substitua por:

```javascript
const linkValue = getChecklistLink(gi, ii);
html += `<div class="ck-item" id="ck-${key}" data-tipo="${item.tipo.toLowerCase()}">
  <div class="ck-item-header" onclick="toggleItem('${key}',event)">
    <span class="ck-item-titulo">${item.tipo}</span>
    <span class="ck-item-badge">${g.badge}</span>
    <span class="ck-item-arrow">▼</span>
  </div>
  <div class="ck-expand">
    <div class="ck-panels">
      <div>
        <span class="ck-panel-label">Jurisprudência</span>
        <div class="ck-panel-content">${escHtml(item.jurisprudencia)}</div>
      </div>
      <div>
        <span class="ck-panel-label">Observações</span>
        <div class="ck-panel-content">${obsFormatted}</div>
      </div>
      <div>
        <span class="ck-panel-label">Link</span>
        <div class="ck-panel-link-wrapper">
          <input class="ck-link-input" type="url" id="ck-link-${key}" value="${escHtml(linkValue)}" placeholder="https://..." onblur="saveChecklistLink(${gi},${ii},this.value)">
          <button class="ck-link-btn" onclick="document.getElementById('ck-link-${key}').focus()">🔗</button>
        </div>
      </div>
    </div>
  </div>
</div>`;
```

- [ ] **Step 3: Adicionar CSS para o painel de link (no bloco `<style>`)**

Procure a seção de estilos CSS (linhas 15-2100 aprox) e adicione antes do `</style>`:

```css
.ck-panel-link-wrapper {
  display: flex;
  gap: 6px;
  align-items: center;
}

.ck-link-input {
  flex: 1;
  background: var(--surface-2);
  border: 1px solid var(--line);
  color: var(--ink);
  padding: 6px 10px;
  border-radius: 6px;
  font-size: 12px;
  font-family: 'Courier New', monospace;
}

.ck-link-input:focus {
  outline: none;
  border-color: var(--line-strong);
  background: var(--surface);
}

.ck-link-btn {
  appearance: none;
  background: transparent;
  border: 1px solid var(--line);
  color: var(--ink);
  width: 32px;
  height: 32px;
  border-radius: 6px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  transition: background 200ms, border-color 200ms;
}

.ck-link-btn:hover {
  background: var(--surface);
  border-color: var(--line-strong);
}
```

- [ ] **Step 4: Testar campo de link no navegador**

Abra o navegador, clique em um item do Checklist para expandir, verifique:
- Campo de link aparece no painel expandido
- Você consegue digitar uma URL
- Ao sair do campo (blur), o link é salvo
- Ao recarregar a página, o link persiste

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: adicionar campo de link em itens do Checklist"
```

---

### Task 5: Corrigir Toggle "Selecionar Todos" em Acompanhamento

**Arquivos:**
- Modificar: `index.html` (função `acompToggleSelectAll()`, linhas ~1240-1260 aprox)

- [ ] **Step 1: Localizar função `acompToggleSelectAll()`**

Procure `function acompToggleSelectAll(checked){` no arquivo.

- [ ] **Step 2: Corrigir lógica do toggle**

Substitua a função inteira por:

```javascript
function acompToggleSelectAll(checked){
  const table = document.querySelector('.acomp-table tbody');
  if(!table) return;
  
  const checkboxes = table.querySelectorAll('input[type="checkbox"]:not(#acomp-check-all)');
  if(checked){
    // Marcar todos
    checkboxes.forEach(cb => {
      cb.checked = true;
      const rowId = cb.closest('tr')?.dataset.id;
      if(rowId) acompSelectedIds.add(rowId);
    });
  } else {
    // Desmarcar todos
    checkboxes.forEach(cb => {
      cb.checked = false;
    });
    acompSelectedIds.clear();
  }
  acompRenderTable();
}
```

- [ ] **Step 3: Testar toggle no navegador**

Abra a seção "ACOMPANHAMENTO", localize o checkbox "Selecionar Todos" (no header da tabela):
- Clique no checkbox → todos os itens da tabela devem ser marcados
- Clique novamente → todos os itens devem ser desmarcados
- Repita para confirmar que funciona nos dois sentidos

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "fix: corrigir toggle 'selecionar todos' em Acompanhamento"
```

---

### Task 6: Adicionar CSS para Sticky Tabs

**Arquivos:**
- Modificar: `index.html` (bloco CSS, linhas ~15-2100 aprox)

- [ ] **Step 1: Procurar classe `.ref-tabs` no CSS**

Procure `.ref-tabs` na seção `<style>`. Se não existir, procure por `ref-tab` para localizar a região.

- [ ] **Step 2: Adicionar/atualizar estilo `.ref-tabs` para sticky**

Adicione ou atualize o estilo:

```css
.ref-tabs {
  position: sticky;
  top: 0;
  z-index: 15;
  background: var(--bg);
  border-bottom: 1px solid var(--line);
  padding: 12px 0;
  display: flex;
  gap: 2px;
  padding-left: clamp(20px, 2.5vw, 40px);
  padding-right: clamp(20px, 2.5vw, 40px);
}
```

- [ ] **Step 3: Testar sticky tabs no navegador**

Abra a seção "JURÍDICO", clique em um item para expandir (ele fica bem grande):
- Ao rolar para baixo dentro do item expandido, a aba superior (CHECKLIST / RECURSOS / RESPOSTAS) deve permanecer visível no topo
- Você deve conseguir clicar em outra aba mesmo com itens expandidos

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "style: adicionar sticky positioning para tabs da seção Jurídico"
```

---

### Task 7: Renomear Desclassificar → Jurídico (Global)

**Arquivos:**
- Modificar: `index.html` (múltiplas seções)

- [ ] **Step 1: Renomear variável global `desclassificarTab` → `juridicoTab`**

Procure `let desclassificarTab = 'checklist';` (por volta de linha 856) e substitua por:

```javascript
let juridicoTab = 'checklist';
```

- [ ] **Step 2: Renomear função `renderDesclassificar()` → `renderJuridico()`**

Procure `function renderDesclassificar(){` (por volta de 2221) e substitua por:

```javascript
function renderJuridico(){
```

Também dentro da função, atualize referências:
- `desclassificarTab===` → `juridicoTab===`
- `switchDesclassificarTab(` → `switchJuridicoTab(`

- [ ] **Step 3: Renomear função `switchDesclassificarTab()` → `switchJuridicoTab()`**

Procure `function switchDesclassificarTab(tab){` (por volta de 2234) e substitua por:

```javascript
function switchJuridicoTab(tab){
  juridicoTab = tab;
  renderJuridico();
  renderSidebar();
}
```

- [ ] **Step 4: Atualizar função `renderChecklist()` para usar `juridicoTab`**

Dentro de `renderChecklist()`, procure por `desclassificarTab===` e substitua por `juridicoTab===`.

- [ ] **Step 5: Renomear no botão de navegação**

Procure `data-section="desclassificar"` (por volta de linha 741) e substitua por:

```html
<button class="nav-btn" data-section="juridico">Jurídico</button>
```

- [ ] **Step 6: Renomear nos títulos e eyebrows das seções**

Em `renderChecklist()`, procure por:
```javascript
<div class="section-eyebrow"><span class="dot"></span>Desclassificar<span class="dot"></span>
```

Substitua por:
```javascript
<div class="section-eyebrow"><span class="dot"></span>Jurídico<span class="dot"></span>
```

Repita para `renderRecursos()` e `renderRespostas()`.

- [ ] **Step 7: Testar no navegador**

Abra o navegador:
- Verifique que o botão na nav diz "JURÍDICO" (não "DESCLASSIFICAR")
- Clique em "JURÍDICO" para abrir a seção
- Verifique que todos os títulos, eyebrows e abas referem-se a "Jurídico"

- [ ] **Step 8: Commit**

```bash
git add index.html
git commit -m "refactor: renomear Desclassificar → Jurídico (global)"
```

---

### Task 8: Teste Final e Validação

**Arquivos:**
- Nenhum arquivo novo (validação manual)

- [ ] **Step 1: Recarregar página e testar fluxo completo**

Abra `index.html` no navegador e teste:

**Checklist:**
- [ ] Grupos colapsáveis funcionam
- [ ] Itens expandem/contraem
- [ ] Campo de link aparece ao expandir
- [ ] Consegue adicionar URL e ela persiste após reload

**Recursos:**
- [ ] Grupos colapsáveis funcionam
- [ ] Itens expandem/contraem
- [ ] Botão "Copiar" funciona
- [ ] Botão "Editar" funciona

**Respostas:**
- [ ] Grupos colapsáveis funcionam
- [ ] Itens expandem/contraem
- [ ] Botão "Copiar" funciona
- [ ] Botão "Editar" funciona

**Tabs Sticky:**
- [ ] Ao expandir item grande, a aba superior permanece visível
- [ ] Consegue clicar em outra aba mesmo com item expandido

**Toggle "Selecionar Todos":**
- [ ] Em Acompanhamento, checkbox "Selecionar Todos" marca todos
- [ ] Clicar novamente desmarca todos

**Renomeação:**
- [ ] Botão na nav diz "JURÍDICO"
- [ ] Todos os títulos/eyebrows referem-se a "Jurídico"

- [ ] **Step 2: Verificar Console do Navegador**

Abra DevTools (F12), aba "Console":
- [ ] Não há erros JS
- [ ] Não há avisos não resolvidos

- [ ] **Step 3: Commit final**

```bash
git add index.html
git commit -m "test: validar refatoração completa da seção Jurídico"
```

---

**Status:** Pronto para execução
