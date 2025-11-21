# Synchronization Guide: Template Builder ↔ Tender Creator

## Overview

This guide explains how to keep the Requisition Lines & Lots functionality synchronized between the **Template Builder** (admin/configuration mode) and the **Tender Creator** (author/usage mode).

## File Locations

- **Template Builder**: `template-builder-workspace.html`
- **Tender Creator**: `tender-create.html`

## Conceptual Mapping

### Template Builder Context
```
Admin configures → Template definition → Preview
```

### Tender Creator Context
```
Template instance → Tender author uses → Actual data
```

## Code Structure Comparison

### State Variables

| Template Builder | Tender Creator | Purpose |
|-----------------|----------------|---------|
| `previewRequisitionLines[blockId]` | `requisitionLines` | Requisition line data |
| `previewLots[blockId]` | `lots` | Lots data |
| `blockConfigurations[blockId]` | N/A (read from template) | Block behavior config |

### Function Mapping

| Template Builder | Tender Creator | Common Logic |
|-----------------|----------------|--------------|
| `initializePreviewRequisitionLines(blockId)` | `initializeRequisitionLines()` | Initialize sample/real data |
| `renderRequisitionLotsPreview(blockId)` | `renderRequisitionLotsSection(...)` | Render UI |
| `createPreviewLot(blockId)` | `createNewLot()` | Create lot |
| `updatePreviewLotName(blockId, lotId, name)` | `updateLotName(lotId, name)` | Update lot name |
| `deletePreviewLot(blockId, lotId)` | `deleteLot(lotId)` | Delete lot |
| `addProductsToPreviewLot(blockId, lotId)` | `addProductsToLot(lotId)` | Add products to lot |
| `removeProductFromPreviewLot(blockId, lotId, idx)` | `removeProductFromLot(lotId, idx)` | Remove product |

## HTML Structure Comparison

### Requisition Lines Table

**Template Builder:**
```html
<div class="preview-container" style="border: 2px dashed ...">
  <div class="requisition-section" style="margin-bottom: ...">
    <table style="width: 100%; border-collapse: collapse; ...">
      <!-- Table content -->
    </table>
  </div>
</div>
```

**Tender Creator:**
```html
<div class="requisition-section">
  <div class="section-subheader">...</div>
  <div class="requisition-table-container">
    <table class="requisition-table">
      <!-- Table content -->
    </table>
  </div>
</div>
```

**Common Elements:**
- Table headers (ID, UNSPSC Code, Description, Quantity, Unit, Amount)
- Row structure with same data fields
- UNSPSC code styling (monospace font, primary color)

### Lot Cards

**Template Builder:**
```html
<div style="border: 1px solid var(--surface-200); ...">
  <div style="display: flex; justify-content: space-between; ...">
    <span style="padding: 4px 10px; background: var(--primary-400); ...">Lot ${lotIndex + 1}</span>
    <input type="text" value="${lot.name}" ... />
  </div>
  <div style="padding: var(--spacing-lg);">
    <!-- Lot products -->
  </div>
</div>
```

**Tender Creator:**
```html
<div class="lot-card" data-lot-id="${lot.id}">
  <div class="lot-header">
    <div class="lot-title">
      <span class="lot-number">Lot ${lotIndex + 1}</span>
      <input type="text" class="lot-name-input" value="${lot.name}" ... />
    </div>
  </div>
  <div class="lot-body">
    <!-- Lot products -->
  </div>
</div>
```

**Common Elements:**
- Lot number badge
- Editable lot name input
- Products list with UNSPSC codes
- Add/remove product buttons

## Synchronization Workflow

When making changes to the Requisition Lines & Lots functionality:

### 1. Identify Change Type

#### UI Changes (HTML/CSS)
- Update both files with same structure
- Adjust class names: `preview-container` wrapper in template builder
- Maintain inline styles in template builder, CSS classes in tender creator

#### Business Logic Changes
- Update function logic in both files
- Adjust parameter lists: template builder adds `blockId` parameter
- Keep core logic identical

#### Data Model Changes
- Update data structures in both files
- Adjust storage: template builder uses `[blockId]` indexing
- Keep data shapes identical

### 2. Apply Change in Tender Creator First

1. Implement and test the change in `tender-create.html`
2. Verify all functionality works correctly
3. Test with real/simulated data

### 3. Adapt for Template Builder

1. Copy the working code from tender creator
2. Add `blockId` parameter to function signatures
3. Update state access patterns:
   ```javascript
   // Tender Creator
   requisitionLines.map(...)
   
   // Template Builder
   previewRequisitionLines[blockId].map(...)
   ```
4. Wrap rendering in preview container
5. Update function names: add `Preview` suffix
6. Make functions global: `window.functionName`

### 4. Update Configuration

If the change requires new configuration options:

1. Add to `blockConfigurations` interface
2. Add UI controls in properties panel
3. Update `applyRequisitionLotsConfig()`
4. Handle in rendering logic

### 5. Test Both Contexts

- [ ] Template builder preview works
- [ ] Tender creator actual functionality works
- [ ] Configuration changes affect preview correctly
- [ ] Data flows correctly from template to tender

## Example: Adding a New Feature

### Scenario: Add "Minimum Lot Size" Validation

#### Step 1: Implement in Tender Creator

**tender-create.html:**
```javascript
// Add to lots data structure
lots = [
  {
    id: 'lot-1',
    name: 'Lot 1',
    unspscCodes: [...],
    minSize: 1  // NEW FIELD
  }
];

// Add validation in addProductsToLot
function addProductsToLot(lotId) {
  const lot = lots.find(l => l.id === lotId);
  if (lot.unspscCodes.length >= (lot.minSize || 1)) {
    // ... existing logic
  } else {
    alert(`Minimum lot size is ${lot.minSize || 1}`);
  }
}

// Add UI for setting minSize
<input type="number" min="1" value="${lot.minSize || 1}" 
       onchange="updateLotMinSize('${lot.id}', this.value)" />

function updateLotMinSize(lotId, minSize) {
  const lot = lots.find(l => l.id === lotId);
  if (lot) lot.minSize = parseInt(minSize);
}
```

#### Step 2: Adapt for Template Builder

**template-builder-workspace.html:**
```javascript
// Add to preview lots data structure
previewLots[blockId] = [
  {
    id: 'lot-1',
    name: 'Lot 1',
    unspscCodes: [...],
    minSize: 1  // NEW FIELD
  }
];

// Add validation in addProductsToPreviewLot
window.addProductsToPreviewLot = function(blockId, lotId) {
  const lots = previewLots[blockId] || [];
  const lot = lots.find(l => l.id === lotId);
  if (lot.unspscCodes.length >= (lot.minSize || 1)) {
    // ... existing logic
  } else {
    alert(`Minimum lot size is ${lot.minSize || 1}`);
  }
}

// Add UI in renderRequisitionLotsPreview
<input type="number" min="1" value="${lot.minSize || 1}" 
       onchange="updatePreviewLotMinSize('${blockId}', '${lot.id}', this.value)" />

window.updatePreviewLotMinSize = function(blockId, lotId, minSize) {
  const lots = previewLots[blockId] || [];
  const lot = lots.find(l => l.id === lotId);
  if (lot) {
    lot.minSize = parseInt(minSize);
  }
}
```

#### Step 3: Add Configuration Option

**template-builder-workspace.html (properties panel):**
```html
<div class="form-group">
  <label for="config-default-min-lot-size">Default Minimum Lot Size</label>
  <input type="number" id="config-default-min-lot-size" class="input" 
         value="1" min="1" />
  <span class="help-text">Minimum number of products required in a lot</span>
</div>
```

**Update blockConfigurations:**
```javascript
blockConfigurations[blockId] = {
  // ... existing config
  defaultMinLotSize: 1,  // NEW CONFIG
};
```

**Apply in createPreviewLot:**
```javascript
window.createPreviewLot = function(blockId) {
  const config = blockConfigurations[blockId] || {};
  const newLot = {
    id: 'lot-' + Date.now(),
    name: lotName,
    unspscCodes: [],
    minSize: config.defaultMinLotSize || 1,  // USE CONFIG
  };
  // ...
}
```

## Common Pitfalls

### 1. Forgetting blockId Parameter

❌ **Wrong:**
```javascript
function createPreviewLot() {
  previewLots.push(newLot);  // Which blockId?
}
```

✅ **Correct:**
```javascript
function createPreviewLot(blockId) {
  if (!previewLots[blockId]) previewLots[blockId] = [];
  previewLots[blockId].push(newLot);
}
```

### 2. Not Making Functions Global

❌ **Wrong:**
```javascript
function addProductsToPreviewLot(blockId, lotId) { ... }
```
```html
<button onclick="addProductsToPreviewLot('${blockId}', '${lotId}')">
<!-- onclick won't find function -->
```

✅ **Correct:**
```javascript
window.addProductsToPreviewLot = function(blockId, lotId) { ... }
```

### 3. Mixing Styles

❌ **Wrong (inconsistent):**
```javascript
// Template builder using tender creator styles
<div class="lot-card" data-lot-id="${lot.id}">
  <!-- CSS class won't exist -->
</div>
```

✅ **Correct:**
```javascript
// Template builder using inline styles
<div style="border: 1px solid var(--surface-200); ..." data-lot-id="${lot.id}">
  <!-- Inline styles work everywhere -->
</div>
```

### 4. Configuration Not Applied

❌ **Wrong:**
```javascript
function renderRequisitionLotsPreview(blockId) {
  // Always show requisition lines, ignoring config
  return `<div class="requisition-section">...</div>`;
}
```

✅ **Correct:**
```javascript
function renderRequisitionLotsPreview(blockId) {
  const config = blockConfigurations[blockId] || {};
  return `
    ${config.displayRequisitionLines ? `
      <div class="requisition-section">...</div>
    ` : ''}
  `;
}
```

## Testing Checklist

When synchronizing changes:

### Template Builder
- [ ] Preview renders correctly with sample data
- [ ] Configuration changes update preview
- [ ] Multiple block instances work independently
- [ ] All interactive elements functional
- [ ] No console errors

### Tender Creator
- [ ] Actual functionality works with real data
- [ ] Configuration from template is respected
- [ ] Validation rules applied correctly
- [ ] Data saves/loads correctly
- [ ] No console errors

### Integration
- [ ] Configuration persists when template saved
- [ ] Template instance uses correct configuration
- [ ] Data flows from template to tender
- [ ] Changes in one don't break the other

## Maintenance Schedule

1. **Before each release**: Verify synchronization
2. **After bug fixes**: Apply to both files
3. **Quarterly**: Review for refactoring opportunities
4. **When adding features**: Follow synchronization workflow

## Future: Component Extraction

To eliminate duplication, extract shared logic:

```javascript
// shared/requisition-lots-components.js
export class RequisitionLotsManager {
  constructor(config, isPreview = false) {
    this.config = config;
    this.isPreview = isPreview;
    this.requisitionLines = [];
    this.lots = [];
  }
  
  renderRequisitionTable() { /* shared logic */ }
  renderLotCard(lot, lotIndex) { /* shared logic */ }
  createLot(name) { /* shared logic */ }
  // ... all shared methods
}

// template-builder-workspace.html
import { RequisitionLotsManager } from './shared/requisition-lots-components.js';
const manager = new RequisitionLotsManager(config, true); // preview mode

// tender-create.html
import { RequisitionLotsManager } from './shared/requisition-lots-components.js';
const manager = new RequisitionLotsManager(config, false); // actual mode
```

This would:
- Eliminate duplication
- Ensure perfect synchronization
- Make testing easier
- Enable code reuse across projects

