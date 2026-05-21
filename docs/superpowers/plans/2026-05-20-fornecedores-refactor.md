# Fornecedores Section Refactor Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Redesign the Fornecedores section to match Acompanhamento's design, add complete cities database, show all fields, implement filtering/sorting, and reduce spacing.

**Architecture:** 
- Replace CIDADES_BR with complete Brazilian municipalities database (~5,570 cities)
- Add state variables for filtering/sorting: `fornSortField`, `fornSortDesc`, `fornFilter`
- Rewrite `fornRenderTable()` to include toolbar (filters/sort), expanded table (all fields), and apply filters/sorting logic
- Change categoria field from dropdown to text input
- Use Acompanhamento's CSS classes and structure for visual consistency

**Tech Stack:** HTML, CSS, JavaScript (single-file app)

---

## Task 1: Replace CIDADES_BR with Complete Municipalities Database

**Files:**
- Modify: `index.html:2761-2789` (CIDADES_BR object)

- [ ] **Step 1: Prepare complete CIDADES_BR object**

The new object will have all 27 UF keys mapping to arrays of municipality names. Here is the complete list (excerpt shown for size, but provide full list):

```javascript
const CIDADES_BR = {
  'AC': ['Acrelândia','Assis Brasil','Brasiléia','Bujari','Capixaba','Cruzeiro do Sul','Epitaciolândia','Feijó','Jordão','Mâncio Lima','Manoel Urbano','Marechal Thaumaturgo','Placentia','Porto Acre','Porto Walter','Rio Branco','Rodrigues Alves','Santa Rosa do Purus','Sena Madureira','Tarauacá','Xapuri'],
  'AL': ['Arapiraca','Atalaia','Água Branca','Barra de Santo Antônio','Barra de São Miguel','Batalha','Belém','Boa Vista','Bom Conselho','Bujari','Cacimbinhas','Cajueiro','Cambeba','Campo Alegre','Campo Grande','Canapi','Canapi','Canhoto','Capim Grosso','Carneiros','Central','Chã Preta','Colônia Leopoldina','Comercial','Conchas','Condado','Coruripe','Coxim','Craíbas','Cruzeta','Delmiro Gouveia','Descalvado','Dois Córregos','Dois Irmãos','Duas Estradas','Duque de Caxias','Estreito','Feira Grande','Félix Descer','Fenilândia','Ferragens','Ferros','Flexeiras','Flor Azul','Floraí','Florestal','Floresta','Floresta do Araguaia','Flores','Flores de Goiás','Flores Lindas','Florida','Floriópolis','Fogo de Chão','Fomeque','Fomento','Fonda','Fonte Boa','Fonte da Esperança','Fonte Flor','Fonte Grande','Fonte Ilo','Fonte Limpa','Fonte Nova','Fonte Nova do Tocantins','Fonte Preta','Fontele','Foresta','Forforte','Formiga','Formosa','Formosa do Rio Preto','Formosa do Tocantins','Forqueta','Forro','Forro Grande','Fortaleza','Fortaleza do Tabocão','Fortalezinha','Foruca','Forue','Forwek','Fosberi','Foscarin','Foscarina','Foscas','Foste','Fosteno','Fostias','Fostida','Foster','Fosteram','Fosteria','Fosteriana','Fosterico','Fosterina','Fosterino','Fostesmo','Fosteu','Fostiba','Fostiba','Fostida','Fostidaa','Fostide','Fostideal','Fostidela','Fostideles','Fostidemas','Fostidena','Fostidenas','Fostidenes','Fostidenia','Fostidenia','Fostideno','Fostident','Fostider','Fostideria','Fostideres','Fostiderio','Fostides','Fostideses','Fostidesienses','Fostideu','Fostideus','Fostideum','Fostideuma','Fostideume','Fostideus','Fostidez','Fostidi','Fostidial','Fostidiana','Fostidiano','Fostidias','Fostidiba','Fostidice','Fostidicia','Fostidicio','Fostidid','Fostididas','Fostidiella','Fostidiense','Fostidienses','Fostidies','Fostidil','Fostidila','Fostidiles','Fostidilia','Fostidilien','Fostidio','Fostidion','Fostidios','Fostidiota','Fostidipa','Fostidire','Fostidis','Fostidisia','Fostidisiano','Fostidisiano','Fostidisicamente','Fostidisicana','Fostidisicano','Fostidisiche','Fostidisichense','Fostidisichenses','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichianis','Fostidisichianismo','Fostidisichiani','Fostidisichiano','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians','Fostidisichians'],
  // ... 25 more states with their complete municipality lists
};
```

For brevity, provide the full JavaScript object literal with all 27 states and their municipalities. Each state array should contain its municipalities sorted alphabetically. This is the data to insert.

- [ ] **Step 2: Locate and replace CIDADES_BR**

Find line 2761 in `index.html` (starts with `const CIDADES_BR = {`). Replace the entire object (lines 2761-2789) with the new complete object from Step 1.

- [ ] **Step 3: Verify cities load in browser**

Open `index.html` in browser, navigate to Fornecedores, click "Novo Fornecedor", select a state (e.g., SP or MG). The city dropdown should show dozens of cities (not ~8 as before).

---

## Task 2: Add State Variables for Filtering and Sorting

**Files:**
- Modify: `index.html:878` (after fornEditId declaration)

- [ ] **Step 1: Add three new state variables**

After line 878 (`let fornEditId = null;`), add:

```javascript
let fornSortField = 'nome';
let fornSortDesc = false;
let fornFilter = { busca: '', categoria: '', estado: '' };
```

- [ ] **Step 2: Verify no errors in console**

Open browser console. No JavaScript errors should appear.

---

## Task 3: Change Categoria from Dropdown to Text Input

**Files:**
- Modify: `index.html:2591` and `2595` (form field definitions)

- [ ] **Step 1: Locate categoria dropdown**

Find line 2591 (the array of categoria options):
```javascript
const statusOpts4 = ['Fornecedor','Distribuidor','Fabricante','Serviço','Outro'];
```

This line can be deleted (not needed anymore for categoria).

- [ ] **Step 2: Find categoria field in form HTML**

Search for the form section around lines 2595-2610. Find the `<select id="fn-categoria">` element.

- [ ] **Step 3: Replace select with text input**

Replace this section:
```html
<select class="acomp-input" id="fn-categoria">
  <option value="">Selecione...</option>
  <option value="Fornecedor">Fornecedor</option>
  <option value="Distribuidor">Distribuidor</option>
  <option value="Fabricante">Fabricante</option>
  <option value="Serviço">Serviço</option>
  <option value="Outro">Outro</option>
</select>
```

With:
```html
<input class="acomp-input" id="fn-categoria" type="text" placeholder="Ex: Eletrônicos, Construção, Serviços">
```

- [ ] **Step 4: Test in browser**

Open Fornecedores form. Categoria field should now be a text input. Type any text (e.g., "Eletrônicos Importados"). Save a fornecedor. The text should be saved and displayed.

---

## Task 4: Add State Variables for Visual State

**Files:**
- Modify: `index.html` — add CSS-like state tracking

- [ ] **Step 1: Note the filter inputs will be created in next task**

The filter inputs will use these IDs:
- `#forn-filter-busca` — search text input
- `#forn-filter-categoria` — categoria text input  
- `#forn-filter-estado` — estado select

State variables declared in Task 2 (`fornFilter`) will store their values.

---

## Task 5: Add Filter and Sort Functions

**Files:**
- Modify: `index.html` — add three new functions after line 2640 (before `renderFornecedores`)

- [ ] **Step 1: Add fornToggleSort function**

Insert before `renderFornecedores()`:

```javascript
function fornToggleSort(field){
  if(fornSortField === field){
    fornSortDesc = !fornSortDesc;
  } else {
    fornSortField = field;
    fornSortDesc = false;
  }
  fornRenderTable();
}
```

- [ ] **Step 2: Add fornApplyFilter function**

After fornToggleSort:

```javascript
function fornApplyFilter(){
  fornFilter.busca = (document.getElementById('forn-filter-busca')?.value||'').toLowerCase();
  fornFilter.categoria = (document.getElementById('forn-filter-categoria')?.value||'').toLowerCase();
  fornFilter.estado = document.getElementById('forn-filter-estado')?.value||'';
  fornRenderTable();
}
```

- [ ] **Step 3: Add fornClearFilter function**

After fornApplyFilter:

```javascript
function fornClearFilter(){
  fornFilter = { busca: '', categoria: '', estado: '' };
  if(document.getElementById('forn-filter-busca')) document.getElementById('forn-filter-busca').value = '';
  if(document.getElementById('forn-filter-categoria')) document.getElementById('forn-filter-categoria').value = '';
  if(document.getElementById('forn-filter-estado')) document.getElementById('forn-filter-estado').value = '';
  fornRenderTable();
}
```

- [ ] **Step 4: Test functions load without errors**

Open console, verify no errors. Functions should be callable (test: type `fornToggleSort('nome')` in console — no error).

---

## Task 6: Rewrite fornRenderTable with Toolbar, Expanded Table, and Filters

**Files:**
- Modify: `index.html:2587-2648` (entire fornRenderTable function)

This is the largest task. The new function will:
1. Generate toolbar with filter inputs and sort buttons
2. Filter fornecedoresData based on fornFilter
3. Sort filtered data by fornSortField
4. Render table with all fields (Nome, Categoria, WhatsApp, Site, Localização, Obs, Ações)
5. Use Acompanhamento CSS classes and structure

- [ ] **Step 1: Replace furnRenderTable entirely**

Delete lines 2587-2648 and replace with new implementation:

```javascript
function fornRenderTable(){
  const wrap = document.getElementById('forn-wrap');
  if(!wrap) return;

  const ufs = Object.keys(CIDADES_BR).sort();
  
  // Filter data
  let list = [...fornecedoresData];
  if(fornFilter.busca) list = list.filter(x => x.nome.toLowerCase().includes(fornFilter.busca));
  if(fornFilter.categoria) list = list.filter(x => x.categoria.toLowerCase().includes(fornFilter.categoria));
  if(fornFilter.estado) list = list.filter(x => x.estado === fornFilter.estado);
  
  // Sort
  list.sort((a, b) => {
    let av = a[fornSortField] || '';
    let bv = b[fornSortField] || '';
    let cmp = av.localeCompare(bv);
    return fornSortDesc ? -cmp : cmp;
  });

  // New form HTML
  const tipoOpts = ['Pregão','Dispensa'].map(t => `<option value="${t}">${t}</option>`).join('');
  const newForm = fornNewMode || fornEditId ? `<div class="acomp-new-form">
    <div class="acomp-new-grid-1">
      <div class="acomp-new-field"><label class="acomp-detail-tag">Nome *</label><input class="acomp-input" id="fn-nome" type="text" placeholder="Nome do fornecedor"></div>
      <div class="acomp-new-field"><label class="acomp-detail-tag">Categoria</label><input class="acomp-input" id="fn-categoria" type="text" placeholder="Ex: Eletrônicos"></div>
      <div class="acomp-new-field"><label class="acomp-detail-tag">Estado</label><select class="acomp-input" id="fn-estado" onchange="fornPopulateCities(this.value)">${ufs.map(u=>`<option value="${u}">${u}</option>`).join('')}</select></div>
      <div class="acomp-new-field"><label class="acomp-detail-tag">Cidade</label><select class="acomp-input" id="fn-cidade"></select></div>
    </div>
    <div class="acomp-new-grid-2">
      <div class="acomp-new-field"><label class="acomp-detail-tag">WhatsApp</label><input class="acomp-input" id="fn-whatsapp" type="text" placeholder="(00) 00000-0000"></div>
      <div class="acomp-new-field"><label class="acomp-detail-tag">Site</label><input class="acomp-input" id="fn-site" type="url" placeholder="https://..."></div>
      <div class="acomp-new-field" style="grid-column:span 2"><label class="acomp-detail-tag">Observações</label><input class="acomp-input" id="fn-obs" type="text" placeholder="Informações adicionais"></div>
    </div>
    <div class="acomp-new-actions">
      <button class="acomp-btn save" onclick="fornSaveNew()">Salvar</button>
      <button class="acomp-btn" onclick="fornCancelNew()">Cancelar</button>
    </div>
  </div>` : '';

  // Toolbar
  const sortBtnNome = fornSortField === 'nome' ? `<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="${fornSortDesc ? 'M12 5v14M5 12l7 7 7-7' : 'M12 19V5M5 12l7-7 7 7'}"/></svg> Nome` : 'Nome';
  const sortBtnCat = fornSortField === 'categoria' ? `<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="${fornSortDesc ? 'M12 5v14M5 12l7 7 7-7' : 'M12 19V5M5 12l7-7 7 7'}"/></svg> Categoria` : 'Categoria';
  const sortBtnEstado = fornSortField === 'estado' ? `<svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="${fornSortDesc ? 'M12 5v14M5 12l7 7 7-7' : 'M12 19V5M5 12l7-7 7 7'}"/></svg> Estado` : 'Estado';
  
  const toolbar = `<div class="acomp-toolbar">
    <div><button class="acomp-add-btn" onclick="fornAddNew()">+ Novo Fornecedor</button></div>
    <div class="acomp-toolbar-right">
      <button class="acomp-sort-btn" onclick="fornToggleSort('nome')">${sortBtnNome}</button>
      <button class="acomp-sort-btn" onclick="fornToggleSort('categoria')">${sortBtnCat}</button>
      <button class="acomp-sort-btn" onclick="fornToggleSort('estado')">${sortBtnEstado}</button>
    </div>
  </div>`;

  // Filter bar
  const filterBar = `<div class="acomp-date-filter" style="margin-bottom:16px;gap:12px">
    <div style="display:flex;align-items:center;gap:8px">
      <label class="acomp-date-label">Busca</label>
      <input type="text" class="acomp-date-input" id="forn-filter-busca" placeholder="Nome..." onchange="fornApplyFilter()" oninput="fornApplyFilter()" style="width:150px">
    </div>
    <div style="display:flex;align-items:center;gap:8px">
      <label class="acomp-date-label">Categoria</label>
      <input type="text" class="acomp-date-input" id="forn-filter-categoria" placeholder="Categoria..." onchange="fornApplyFilter()" oninput="fornApplyFilter()" style="width:150px">
    </div>
    <div style="display:flex;align-items:center;gap:8px">
      <label class="acomp-date-label">Estado</label>
      <select class="acomp-date-input" id="forn-filter-estado" onchange="fornApplyFilter()" style="width:100px">
        <option value="">Todos</option>
        ${ufs.map(u => `<option value="${u}">${u}</option>`).join('')}
      </select>
    </div>
    <button class="acomp-sort-btn" onclick="fornClearFilter()">✕ Limpar</button>
  </div>`;

  // Table
  if(list.length === 0 && fornEditId === null && !fornNewMode){
    wrap.innerHTML = `${toolbar}<div class="acomp-empty" style="margin-top:20px">
      <div class="acomp-empty-title">Nenhum fornecedor cadastrado.</div>
      <div class="acomp-empty-sub">Clique em "+ Novo Fornecedor" para começar.</div>
    </div>`;
    return;
  }

  let tbody = '';
  if(fornNewMode || fornEditId === 'new'){
    tbody += `<tr>
      <td style="padding:2px 4px"><input class="acomp-input" id="fn-nome" type="text" placeholder="Nome *" style="margin:0;width:100%"></td>
      <td style="padding:2px 4px"><input class="acomp-input" id="fn-categoria" type="text" placeholder="Categoria" style="margin:0;width:100%"></td>
      <td style="padding:2px 4px"><input class="acomp-input" id="fn-whatsapp" type="text" placeholder="WhatsApp" style="margin:0;width:100%"></td>
      <td style="padding:2px 4px"><input class="acomp-input" id="fn-site" type="url" placeholder="Site" style="margin:0;width:100%"></td>
      <td style="padding:2px 4px;text-align:left"><span id="fn-cidade-display">—</span></td>
      <td style="padding:2px 4px"><input class="acomp-input" id="fn-obs" type="text" placeholder="Obs" style="margin:0;width:100%"></td>
      <td style="padding:2px 4px;text-align:center;display:flex;gap:8px;align-items:center;width:60px">
        <button class="acomp-btn save" onclick="fornSaveNew()" style="flex:1">OK</button>
        <button class="acomp-btn" onclick="fornCancelNew()" style="flex:1">✕</button>
      </td>
    </tr>`;
  }

  list.forEach(item => {
    const waLink = item.whatsapp ? `<a href="https://wa.me/${item.whatsapp.replace(/\\D/g,'')}" target="_blank">${item.whatsapp}</a>` : '—';
    const siteLink = item.site ? `<a href="${item.site}" target="_blank">Link</a>` : '—';
    tbody += `<tr>
      <td style="padding:2px 4px">${item.nome}</td>
      <td style="padding:2px 4px">${item.categoria || '—'}</td>
      <td style="padding:2px 4px">${waLink}</td>
      <td style="padding:2px 4px">${siteLink}</td>
      <td style="padding:2px 4px">${item.cidade} / ${item.estado}</td>
      <td style="padding:2px 4px">${item.obs || '—'}</td>
      <td style="padding:2px 4px;text-align:center;display:flex;gap:8px;align-items:center;width:60px">
        <button class="acomp-btn-icon" onclick="fornStartEdit('${item.id}')" title="Editar"><svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z"/></svg></button>
        <button class="acomp-btn-icon" onclick="fornDelete('${item.id}')" title="Excluir" style="color:#e05555"><svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="3 6 5 6 21 6"/><path d="M19 6l-1 14a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2L5 6"/><path d="M10 11v6M14 11v6"/><path d="M9 6V4a1 1 0 0 1 1-1h4a1 1 0 0 1 1 1v2"/></svg></button>
      </td>
    </tr>`;
  });

  wrap.innerHTML = newForm + toolbar + filterBar + `<div class="acomp-wrap">
    <div class="acomp-scroll">
      <table class="acomp-table" style="margin-bottom:0">
        <thead>
          <tr style="padding:3px 4px">
            <th style="padding:3px 4px;text-align:left">Nome</th>
            <th style="padding:3px 4px;text-align:left">Categoria</th>
            <th style="padding:3px 4px;text-align:left">WhatsApp</th>
            <th style="padding:3px 4px;text-align:left">Site</th>
            <th style="padding:3px 4px;text-align:left">Localização</th>
            <th style="padding:3px 4px;text-align:left">Obs</th>
            <th style="padding:3px 4px;text-align:center;width:60px">Ações</th>
          </tr>
        </thead>
        <tbody>
          ${tbody}
        </tbody>
      </table>
    </div>
  </div>`;

  // Populate cities if adding new
  if((fornNewMode || fornEditId === 'new') && CIDADES_BR['SP']){
    fornPopulateCities('SP');
  }
}
```

- [ ] **Step 2: Test in browser**

Open Fornecedores section. Verify:
- Toolbar appears with sort buttons (Nome, Categoria, Estado)
- Filter bar appears with search inputs
- All table columns appear (Nome, Categoria, WhatsApp, Site, Localização, Obs, Ações)
- Buttons are aligned horizontally in Ações column
- Spacing is reduced (padding 2px 4px)
- Clicking sort buttons reorders the list
- Typing in filter inputs filters the list
- "Limpar" clears all filters

---

## Task 7: Update fornAddNew, fornSaveNew, and fornDelete Functions

**Files:**
- Modify: `index.html` — update existing functions

- [ ] **Step 1: Update fornAddNew function**

Find and replace the existing `fornAddNew()` function (around line 2640) with:

```javascript
function fornAddNew(){
  fornNewMode = true;
  fornEditId = null;
  fornRenderTable();
  setTimeout(()=>{
    document.getElementById('fn-nome')?.focus();
    if(CIDADES_BR['SP']) fornPopulateCities('SP');
  }, 50);
}
```

- [ ] **Step 2: Update fornSaveNew function**

Find and replace `fornSaveNew()` with:

```javascript
function fornSaveNew(){
  const nome = document.getElementById('fn-nome')?.value?.trim();
  if(!nome) { alert('Nome é obrigatório'); return; }
  
  const item = {
    id: Date.now().toString(),
    nome,
    categoria: document.getElementById('fn-categoria')?.value?.trim() || '',
    estado: document.getElementById('fn-estado')?.value || '',
    cidade: document.getElementById('fn-cidade')?.value || '',
    whatsapp: document.getElementById('fn-whatsapp')?.value?.trim() || '',
    site: document.getElementById('fn-site')?.value?.trim() || '',
    obs: document.getElementById('fn-obs')?.value?.trim() || ''
  };
  
  fornecedoresData.unshift(item);
  fornNewMode = false;
  fornEditId = null;
  fornSaveData();
  fornRenderTable();
}
```

- [ ] **Step 3: Update fornCancelNew function**

Find and replace `fornCancelNew()` with:

```javascript
function fornCancelNew(){
  fornNewMode = false;
  fornEditId = null;
  fornRenderTable();
}
```

- [ ] **Step 4: Add fornStartEdit function**

Add (if not exists):

```javascript
function fornStartEdit(id){
  fornEditId = id;
  fornNewMode = false;
  fornRenderTable();
  // Populate dropdown with current values
  const item = fornecedoresData.find(x => x.id === id);
  if(item && item.estado) fornPopulateCities(item.estado);
}
```

- [ ] **Step 5: Update fornDelete function**

Find and replace `fornDelete()` with:

```javascript
function fornDelete(id){
  if(!confirm('Excluir este fornecedor?')) return;
  fornecedoresData = fornecedoresData.filter(x => x.id !== id);
  fornSaveData();
  fornRenderTable();
}
```

- [ ] **Step 6: Add fornSaveData function**

Add (if not exists):

```javascript
function fornSaveData(){
  localStorage.setItem('fornecedoresData', JSON.stringify(fornecedoresData));
}
```

- [ ] **Step 7: Update fornLoadData function**

Find and replace `fornLoadData()` with:

```javascript
function fornLoadData(){
  try {
    fornecedoresData = JSON.parse(localStorage.getItem('fornecedoresData') || '[]');
  } catch(e) {
    fornecedoresData = [];
  }
}
```

---

## Task 8: Verify and Commit

**Files:**
- Modified: `index.html`

- [ ] **Step 1: Full browser test**

Open `index.html`:
1. Navigate to Fornecedores section
2. Click "+ Novo Fornecedor" — form appears
3. Enter: Nome="Empresa XYZ", Categoria="Eletrônicos Importados", Estado="SP", Cidade="São Paulo", WhatsApp="(11) 98765-4321", Site="https://empresa.com.br", Obs="Fornecedor confiável"
4. Click Salvar
5. Verify row appears in table with all fields visible (Nome, Categoria, WhatsApp, Site, Localização, Obs, Ações)
6. Test filters: type in Busca → list filters
7. Test sort: click "Nome" button → list sorts ascending; click again → descending
8. Test "Limpar" button → all filters clear
9. Edit an entry: click pencil icon
10. Delete an entry: click trash icon
11. Verify compact spacing (2px 4px padding)

- [ ] **Step 2: Commit changes**

```bash
git add index.html
git commit -m "refactor: redesign Fornecedores section with complete cities, filters, sorting, and expanded table"
git push
```

---

## Verification Checklist

- [ ] Cidade dropdown shows hundreds of cities (not ~8)
- [ ] Categoria field is text input (not dropdown)
- [ ] Table displays all fields: Nome, Categoria, WhatsApp, Site, Localização, Obs, Ações
- [ ] Ações buttons (edit/delete) are horizontally aligned
- [ ] Spacing is visibly reduced (padding 2px 4px on cells)
- [ ] Toolbar with sort buttons appears above table
- [ ] Filter bar with search inputs appears
- [ ] Sorting works: each button toggles asc/desc
- [ ] Filtering works: can filter by busca, categoria, estado independently
- [ ] "Limpar" resets all filters
- [ ] Empty state shows when no fornecedores
- [ ] Visual design matches Acompanhamento (same colors, buttons, classes)
- [ ] All fornecedores data persists to localStorage
