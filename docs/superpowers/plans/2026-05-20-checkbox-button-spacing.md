# Checkbox and Button Spacing Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Improve visual layout by moving checkboxes right and standardizing action button sizes to match the LINK button.

**Architecture:** Three CSS modifications in index.html:
1. Increase checkbox padding to better distribute table space
2. Scale action buttons (eye, lock, edit, delete) from 22×22px to 28×28px
3. Increase gap between action buttons from 4px to 8px
4. Scale internal SVG icons from 12×12px to 13×13px

**Tech Stack:** HTML, CSS, SVG (single-file application)

---

## Task 1: Update Checkbox Padding

**Files:**
- Modify: `index.html:469` (CSS rule `.acomp-select-check`)

- [ ] **Step 1: Locate the checkbox CSS rule**

Find line 469 in `index.html`:
```css
.acomp-select-check { width:14px; height:14px; cursor:pointer; accent-color:var(--ink) }
```

- [ ] **Step 2: Add padding property**

Change the rule to:
```css
.acomp-select-check { width:14px; height:14px; padding:4px 10px; cursor:pointer; accent-color:var(--ink) }
```

This increases left/right padding from 2px to 10px, moving the checkbox away from the left edge.

- [ ] **Step 3: Verify in browser**

Open `index.html` in browser. Checkbox should now be more centered in its cell, with visible space on both sides.

---

## Task 2: Increase Action Button Size

**Files:**
- Modify: `index.html:524-529` (CSS rule `.acomp-btn-icon`)

- [ ] **Step 1: Locate the action button CSS rule**

Find lines 524-529 in `index.html`:
```css
.acomp-btn-icon {
  width: 22px;
  height: 22px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 9px;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid var(--line);
  background: transparent;
  color: var(--muted);
  transition: background 140ms, color 140ms;
}
```

- [ ] **Step 2: Update width and height**

Change `width: 22px;` to `width: 28px;` and `height: 22px;` to `height: 28px;`

- [ ] **Step 3: Update font-size**

Change `font-size: 9px;` to `font-size: 13px;`

**Result:**
```css
.acomp-btn-icon {
  width: 28px;
  height: 28px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 13px;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid var(--line);
  background: transparent;
  color: var(--muted);
  transition: background 140ms, color 140ms;
}
```

- [ ] **Step 4: Verify button size matches LINK button**

Open `index.html` in browser. Action buttons (eye, lock, edit, delete) should now be same size as LINK (globe) button.

---

## Task 3: Increase Gap Between Action Buttons

**Files:**
- Modify: `index.html:536` (CSS rule `.acomp-actions-cell`)

- [ ] **Step 1: Locate the actions cell CSS rule**

Find line 536 in `index.html`:
```css
.acomp-actions-cell { white-space: nowrap; display: flex; gap: 4px; align-items: center; }
```

- [ ] **Step 2: Update gap value**

Change `gap: 4px;` to `gap: 8px;`

**Result:**
```css
.acomp-actions-cell { white-space: nowrap; display: flex; gap: 8px; align-items: center; }
```

- [ ] **Step 3: Verify spacing in browser**

Open `index.html` in browser. Action buttons should have more space between them (8px instead of 4px).

---

## Task 4: Increase SVG Icon Sizes

**Files:**
- Modify: `index.html:990-993` (SVG icons in action buttons)

- [ ] **Step 1: Locate the eye icon SVG (line 990)**

Find this line:
```html
<button class="acomp-btn-icon" title="Ver">
  <svg viewBox="0 0 24 24" width="12" height="12" fill="currentColor">
```

Change `width="12" height="12"` to `width="13" height="13"`

**Result:**
```html
<button class="acomp-btn-icon" title="Ver">
  <svg viewBox="0 0 24 24" width="13" height="13" fill="currentColor">
```

- [ ] **Step 2: Locate the lock icon SVG (line 991)**

Find this line:
```html
<button class="acomp-btn-icon" title="Bloquear">
  <svg viewBox="0 0 24 24" width="12" height="12" fill="currentColor">
```

Change `width="12" height="12"` to `width="13" height="13"`

**Result:**
```html
<button class="acomp-btn-icon" title="Bloquear">
  <svg viewBox="0 0 24 24" width="13" height="13" fill="currentColor">
```

- [ ] **Step 3: Locate the edit icon SVG (line 992)**

Find this line:
```html
<button class="acomp-btn-icon" title="Editar">
  <svg viewBox="0 0 24 24" width="12" height="12" fill="currentColor">
```

Change `width="12" height="12"` to `width="13" height="13"`

**Result:**
```html
<button class="acomp-btn-icon" title="Editar">
  <svg viewBox="0 0 24 24" width="13" height="13" fill="currentColor">
```

- [ ] **Step 4: Locate the delete icon SVG (line 993)**

Find this line:
```html
<button class="acomp-btn-icon" title="Excluir">
  <svg viewBox="0 0 24 24" width="12" height="12" fill="currentColor">
```

Change `width="12" height="12"` to `width="13" height="13"`

**Result:**
```html
<button class="acomp-btn-icon" title="Excluir">
  <svg viewBox="0 0 24 24" width="13" height="13" fill="currentColor">
```

- [ ] **Step 5: Verify icon sizes in browser**

Open `index.html` in browser. All action button icons (eye, lock, edit, delete) should now be slightly larger and consistent.

---

## Task 5: Final Verification and Commit

**Files:**
- Modified: `index.html`

- [ ] **Step 1: Full visual verification**

Open `index.html` in browser and verify:
- Checkbox is moved to the right (more padding on left side)
- All action buttons (eye, lock, edit, delete) are same size as LINK button (globe)
- Icons inside buttons are larger and consistent
- Spacing between action buttons is noticeably increased
- Table maintains good proportions and space distribution

- [ ] **Step 2: Test in different table states**

- Click "select all" checkbox and verify checkboxes appear with proper padding
- Expand a row (click dropdown) and verify action buttons display correctly
- Scroll horizontally (if needed) and verify buttons remain properly spaced

- [ ] **Step 3: Commit changes**

```bash
git add index.html
git commit -m "style: aumentar espaçamento e tamanho dos botões de ação"
git push
```

---

## Verification Checklist

- [ ] Checkbox has visible left padding (not flush against edge)
- [ ] All action buttons are 28×28px (same as LINK button)
- [ ] Icons are 13×13px in all action buttons
- [ ] Gap between action buttons is 8px
- [ ] No visual regressions in table layout
- [ ] Changes committed and pushed
