# Requisition Lines & Lots Integration

## Overview

This document describes the integration of the **Requisition Lines & Lots** functionality from `tender-create.html` into `template-builder-workspace.html`, adapted for template configuration by admin users.

## Architecture

### Two Modes of Operation

1. **Template Configuration Mode (Admin)** - `template-builder-workspace.html`
   - Admins configure the block structure, behavior, and defaults
   - Define which features are available to tender authors
   - Configure data sources and validation rules
   - Preview how the block will appear to tender authors

2. **Tender Authoring Mode (Author)** - `tender-create.html`
   - Tender authors use the configured template to create actual tenders
   - Interact with live data from ERP systems
   - Create and manage lots based on template configuration
   - Define product requirements per UNSPSC code

## Implementation Details

### Data Structures

#### Template Builder State
```javascript
// Stores preview data per block instance
previewRequisitionLines = {
  'block-id': [
    { id, unspscCode, unspscDescription, description, quantity, unitOfMeasure, amount, currency }
  ]
}

previewLots = {
  'block-id': [
    { id, name, unspscCodes: [{ code, description }] }
  ]
}

// Stores configuration per block instance
blockConfigurations = {
  'block-id': {
    displayRequisitionLines: boolean,
    enableLotCreation: boolean,
    lotAssignmentRules: 'flexible' | 'strict',
    dataSource: 'erp' | 'pdj' | 'manual'
  }
}
```

### Key Functions

#### Template Builder Functions

1. **`initializePreviewRequisitionLines(blockId)`**
   - Initializes sample requisition lines for preview
   - Creates default configuration if not exists
   - Called when block is first added to canvas

2. **`renderRequisitionLotsPreview(blockId)`**
   - Renders the preview of what tender authors will see
   - Respects configuration settings
   - Shows/hides sections based on config

3. **`createPreviewLot(blockId)`**
   - Creates a new lot in the preview
   - Demonstrates lot creation functionality to admin

4. **`applyRequisitionLotsConfig()`**
   - Applies configuration changes from properties panel
   - Refreshes the preview to reflect changes

#### Shared Functions (Global Scope)

All preview interaction functions are attached to `window` object:
- `window.createPreviewLot(blockId)`
- `window.updatePreviewLotName(blockId, lotId, newName)`
- `window.deletePreviewLot(blockId, lotId)`
- `window.addProductsToPreviewLot(blockId, lotId)`
- `window.removeProductFromPreviewLot(blockId, lotId, index)`

### Block Configuration

When the Requisition Lines & Lots block is selected in the template builder, the properties panel shows:

#### Configuration Options

1. **Display Requisition Lines**
   - Checkbox: Show/hide requisition lines table in tender authoring
   - Default: `true`

2. **Enable Lot Creation**
   - Checkbox: Allow tender authors to create and manage lots
   - Default: `true`

3. **Lot Assignment Rules**
   - Select: `flexible` or `strict`
   - `flexible`: Authors can add any UNSPSC code to lots
   - `strict`: Authors can only add UNSPSC codes from requisition lines

4. **Requisition Line Data Source**
   - Select: `erp`, `pdj`, or `manual`
   - Determines where requisition line data comes from

## Block Structure

### Subsections

The Requisition Lines & Lots block contains two main subsections:

1. **Block Configuration**
   - Contains fields for configuring block behavior
   - Visible to admins only
   - Fields:
     - Display Requisition Lines
     - Enable Lot Creation
     - Lot Assignment Rules
     - Default Lot Behavior
     - Requisition Line Data Source

2. **Preview: Tender Author View**
   - Shows interactive preview of tender author experience
   - Updates dynamically based on configuration
   - Includes:
     - Info alert explaining preview context
     - Requisition lines table (if enabled)
     - Lots management section (if enabled)
     - Tender structure summary

## UI Components

### Preview Container

The preview is rendered in a dashed border container to visually distinguish it from editable configuration:

```html
<div class="preview-container" style="border: 2px dashed var(--surface-300); ...">
  <!-- Preview content -->
</div>
```

### Configuration Info Alert

```html
<div style="padding: ...; background: var(--info-50); border: 1px solid var(--info-400); ...">
  <div style="...">
    <span>ℹ️</span>
    <span>This is a preview of what tender authors will see...</span>
  </div>
</div>
```

## Integration Points

### Adding Block to Canvas

When dragged from components panel:
1. Block data structure includes `isPreviewSection: true` flag
2. `addSectionToCanvas()` checks for special section types
3. Preview section renders using `renderRequisitionLotsPreview()`
4. Configuration is initialized with defaults

### Properties Panel Integration

When block is selected:
1. `selectBlock()` checks if type is `'requisition-lots'`
2. Shows Requisition Lines & Lots specific configuration
3. Hides general block configuration options
4. Populates configuration values from `blockConfigurations`

### Configuration Application

When admin clicks "Apply Configuration to Preview":
1. `applyRequisitionLotsConfig()` reads form values
2. Updates `blockConfigurations[blockId]`
3. Calls `refreshRequisitionLotsPreview(blockId)`
4. Shows success confirmation

## Styling

### Custom CSS Classes

```css
.preview-container {
  /* Dashed border container for preview */
}

.preview-container .btn {
  /* Button styles within preview */
}

.preview-container .icon-btn {
  /* Icon button styles within preview */
}
```

## Usage Flow

### For Template Admins

1. Drag "Requisition Lines & Lots" block from components panel to canvas
2. Click on the block to select it
3. Configure options in properties panel:
   - Toggle requisition lines display
   - Enable/disable lot creation
   - Set lot assignment rules
   - Choose data source
4. Click "Apply Configuration to Preview"
5. Interact with preview to test user experience
6. Save template for tender authors to use

### For Tender Authors (in tender-create.html)

1. Initiate tender using template with Requisition Lines & Lots block
2. View requisition lines from ERP (if configured)
3. Create lots if enabled by template
4. Add UNSPSC codes to lots (following configured rules)
5. View tender structure summary
6. Proceed to define product requirements per UNSPSC code

## Data Flow

```
Template Configuration (Admin)
    ↓
blockConfigurations[blockId]
    ↓
Template Definition (JSON/Database)
    ↓
Tender Creation (Author)
    ↓
Actual Tender Data
```

## Maintainability

### Shared Logic Extraction

Both `template-builder-workspace.html` and `tender-create.html` share:
- HTML structure for requisition lines table
- Lot card rendering logic
- UNSPSC code management
- Tender structure summary

**Future Enhancement**: Extract shared components into reusable modules:
```javascript
// requisition-lots-components.js
export function renderRequisitionTable(requisitionLines) { ... }
export function renderLotCard(lot, lotIndex, handlers) { ... }
export function renderStructureSummary(requisitionLines, lots) { ... }
```

### Configuration Schema

Define a clear schema for block configuration:
```typescript
interface RequisitionLotsConfig {
  displayRequisitionLines: boolean;
  enableLotCreation: boolean;
  lotAssignmentRules: 'flexible' | 'strict';
  dataSource: 'erp' | 'pdj' | 'manual';
}
```

### Validation

Add validation to ensure:
- At least one option is enabled (requisition lines OR lot creation)
- Data source is compatible with other settings
- Configuration changes don't break existing templates

## Testing Checklist

### Template Builder

- [ ] Drag Requisition Lines & Lots block to canvas
- [ ] Block appears with both subsections
- [ ] Preview shows sample data correctly
- [ ] Configuration panel shows when block selected
- [ ] Toggle "Display Requisition Lines" and verify preview updates
- [ ] Toggle "Enable Lot Creation" and verify preview updates
- [ ] Change lot assignment rules and verify behavior
- [ ] Create preview lot and add products
- [ ] Delete preview lot
- [ ] Multiple instances of block work independently

### Integration

- [ ] Configuration persists when switching between blocks
- [ ] Preview refreshes correctly after configuration changes
- [ ] No console errors when interacting with preview
- [ ] Properties panel shows correct values when block reselected
- [ ] Block can be moved, collapsed, and deleted
- [ ] Template can be saved with configured block

## Known Limitations

1. **Preview Data**: Currently uses hardcoded sample data. Real integration would fetch from actual ERP test environment.

2. **Configuration Persistence**: Configuration is stored in memory only. Need to implement serialization for template saving.

3. **Validation**: Limited validation of configuration combinations. Need to add business rule validation.

4. **Reusability**: Code is duplicated between template builder and tender creator. Should be extracted to shared components.

## Future Enhancements

1. **Live Data Preview**: Connect to ERP test environment for realistic preview data

2. **Configuration Templates**: Provide preset configurations for common scenarios

3. **Validation Rules**: Add configurable validation rules for UNSPSC codes and lot assignments

4. **Conditional Logic**: Support conditional visibility based on other form values

5. **Internationalization**: Support multiple languages in configuration and preview

6. **Audit Trail**: Track configuration changes and who made them

7. **Version Control**: Support versioning of block configurations

## References

- Original implementation: `tender-create.html` (lines 2146-3469)
- Template builder integration: `template-builder-workspace.html` (lines 1618-2320)
- Design system: `pdj-frontend.mdc`

