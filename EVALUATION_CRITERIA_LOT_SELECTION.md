# Evaluation Criteria - Lot/Product Selection Feature

## Overview

The **Evaluation Criteria** section now includes an enhanced feature that allows procurement officers to specify whether each evaluation criterion applies to:
1. **The entire tender** (all lots/products)
2. **Specific lot(s)** (select entire lots)
3. **Specific product(s)** (select individual UNSPSC codes)

This provides granular control over how different parts of a tender are evaluated, particularly useful for multi-lot tenders or tenders with diverse product categories.

## Key Features

### 1. **Entire Tender Application**
- Single selection applies criterion to all lots and products
- Simplest option for uniform evaluation
- Best for criteria that apply universally (e.g., "Vendor must be registered")

### 2. **Lot-Based Selection**
- Select one or multiple lots
- Automatically includes all products within selected lots
- Efficient for lot-level criteria (e.g., "Lot 1 requires ISO certification")
- Shows product count per lot for clarity

### 3. **Product-Based Selection**
- Granular selection at UNSPSC code level
- Can select individual products across different lots
- Useful for product-specific requirements (e.g., "Medical equipment must be FDA approved")
- Shows which lot each product belongs to

### 4. **Hybrid Selection**
- Combine lot-based and product-based selection
- Select entire lots PLUS additional individual products
- Maximum flexibility for complex evaluation scenarios
- System automatically merges selections without duplication

### 5. **Visual Selection Summary**
- Real-time display of selection status
- Shows number of lots selected
- Shows total number of products affected
- Clear visual feedback in info panel

## User Interface

### Criterion Configuration Card
```
┌─────────────────────────────────────────────────────────────────┐
│ Criterion 1                                              [×]     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│ Type: [Technical criteria                           ▼]          │
│ Evaluation Method: [Scoring (0-100)                 ▼]          │
│                                                                  │
│ Description:                                                     │
│ [Describe the evaluation criterion...                    ]      │
│                                                                  │
│ Applies To:                                                      │
│ ○ Entire Tender                                                 │
│ ● Specific Lot(s) or Product(s)                                │
│                                                                  │
│ ┌─────────────────────────────────────────────────────────┐    │
│ │ SELECT BY LOT                                            │    │
│ │ [Lot 1 - Medical Equipment (15 products)          ]     │    │
│ │ [Lot 2 - Office Supplies (8 products)             ]     │    │
│ │ [Lot 3 - IT Equipment (12 products)               ]     │    │
│ │                                                          │    │
│ │ Hold Ctrl/Cmd to select multiple lots                   │    │
│ └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│ ┌─────────────────────────────────────────────────────────┐    │
│ │ OR SELECT INDIVIDUAL PRODUCTS (UNSPSC CODES)             │    │
│ │ [42182000 - Medical diagnostic equipment [In: Lot 1]]   │    │
│ │ [42192000 - Surgical instruments [In: Lot 1]       ]    │    │
│ │ [43211500 - Desktop computers [In: Lot 3]          ]    │    │
│ │ [44121700 - Writing paper [From requisition]       ]    │    │
│ │                                                          │    │
│ │ Hold Ctrl/Cmd to select multiple products                │    │
│ └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│ ┌─────────────────────────────────────────────────────────┐    │
│ │ 📊 CURRENT SELECTION:                                    │    │
│ │ ✓ 2 lot(s) selected                                     │    │
│ │ ✓ 25 product(s) will be evaluated with this criterion   │    │
│ └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

## Data Structure

### Criterion Object
```javascript
{
  id: 'ec_1',
  type: 'Technical criteria',
  description: 'Product must meet ISO standards',
  evaluationMethod: 'Pass/Fail',
  appliesTo: 'specific', // 'entire' | 'specific'
  lots: ['lot_1', 'lot_2'], // Array of lot IDs
  unspscCodes: ['42182000', '42192000', '43211500'] // Array of UNSPSC codes
}
```

### Lot Object Reference
```javascript
{
  id: 'lot_1',
  name: 'Medical Equipment',
  unspscCodes: [
    { code: '42182000', description: 'Medical diagnostic equipment' },
    { code: '42192000', description: 'Surgical instruments' }
  ]
}
```

## User Workflows

### Workflow 1: Apply Criterion to Entire Tender
1. Add new evaluation criterion
2. Configure type, method, and description
3. Select "Entire Tender" radio button
4. ✅ Done - criterion applies to all lots/products

### Workflow 2: Apply Criterion to Specific Lots
1. Add new evaluation criterion
2. Configure type, method, and description
3. Select "Specific Lot(s) or Product(s)" radio button
4. In "SELECT BY LOT" dropdown, select one or more lots (Ctrl/Cmd + click)
5. System automatically includes all products in selected lots
6. View summary showing lot count and product count
7. ✅ Done - criterion applies to selected lots

### Workflow 3: Apply Criterion to Specific Products
1. Add new evaluation criterion
2. Configure type, method, and description
3. Select "Specific Lot(s) or Product(s)" radio button
4. In "SELECT INDIVIDUAL PRODUCTS" dropdown, select specific UNSPSC codes
5. View which lot each product belongs to (shown in brackets)
6. View summary showing product count
7. ✅ Done - criterion applies to selected products

### Workflow 4: Hybrid Selection (Lots + Individual Products)
1. Add new evaluation criterion
2. Configure type, method, and description
3. Select "Specific Lot(s) or Product(s)" radio button
4. In "SELECT BY LOT", select one or more lots
5. ALSO in "SELECT INDIVIDUAL PRODUCTS", select additional products
6. System merges selections (no duplicates)
7. View comprehensive summary
8. ✅ Done - criterion applies to combined selection

## Use Cases

### Use Case 1: Quality Certification by Product Category
**Scenario:** Medical equipment requires FDA approval, but office supplies do not.

**Solution:**
- Criterion 1: "FDA Approval Required"
  - Type: Qualification criteria
  - Applies To: Specific Lot → Select "Lot 1 - Medical Equipment"

### Use Case 2: Technical Specifications for IT Products
**Scenario:** Only computers and servers need Energy Star certification.

**Solution:**
- Criterion 1: "Energy Star Certification"
  - Type: Technical criteria
  - Applies To: Specific Products → Select:
    - 43211500 - Desktop computers
    - 43211600 - Servers
    - 43211700 - Laptops

### Use Case 3: Delivery Requirements by Lot
**Scenario:** Lot 1 needs delivery within 30 days, Lot 2 needs 60 days.

**Solution:**
- Criterion 1: "Delivery within 30 days"
  - Type: Technical criteria
  - Applies To: Specific Lot → Select "Lot 1"
- Criterion 2: "Delivery within 60 days"
  - Type: Technical criteria
  - Applies To: Specific Lot → Select "Lot 2"

### Use Case 4: General Eligibility for All
**Scenario:** All vendors must be registered regardless of lot/product.

**Solution:**
- Criterion 1: "Valid business registration"
  - Type: Eligibility and formal criteria
  - Applies To: Entire Tender

### Use Case 5: Mixed Lot and Product Selection
**Scenario:** Entire Lot 1 needs ISO certification, plus specific high-value items from Lot 2.

**Solution:**
- Criterion 1: "ISO 9001 Certification"
  - Type: Qualification criteria
  - Applies To: Specific Lot(s) or Product(s) → Select:
    - Lot: "Lot 1 - Medical Equipment"
    - Products: "43211600 - Servers" from Lot 2

## Technical Implementation

### Functions Added

#### `updateEvaluationCriterionLots(criterionId, selectElement)`
- Handles lot selection changes
- Updates criterion's lots array
- Auto-populates unspscCodes from selected lots
- Prevents duplication

#### Enhanced `updateEvaluationCriterionUnspscCodes(criterionId, selectElement)`
- Handles individual product selection
- Works alongside lot selection
- Allows granular control

### Selection Logic
```javascript
// When lots are selected
criterion.lots = ['lot_1', 'lot_2'];

// System automatically includes all products from those lots
criterion.unspscCodes = [];
criterion.lots.forEach(lotId => {
  const lot = lots.find(l => l.id === lotId);
  if (lot) {
    lot.unspscCodes.forEach(unspsc => {
      if (!criterion.unspscCodes.includes(unspsc.code)) {
        criterion.unspscCodes.push(unspsc.code);
      }
    });
  }
});

// Additional individual products can be added
// System ensures no duplicates
```

### UI Features

#### Contextual Help Text
- Clear instructions for each selection method
- Keyboard shortcuts displayed (Ctrl/Cmd)
- Lot information shown with product counts
- Product origins indicated (which lot or from requisition)

#### Visual Feedback
- Selected items highlighted
- Real-time selection summary
- Color-coded info panels
- Hover states and transitions

#### Empty States
- Warning when no lots/products available
- Helpful guidance to add requisition lines or create lots
- Clear next steps

## Validation Rules

### Required Validations
1. **Applies To must be selected** - Radio button required
2. **If "Specific" selected, at least one lot or product must be chosen**
3. **Type and Evaluation Method must be selected**
4. **Description must not be empty**

### Business Rules
1. Lot selection automatically includes all products in that lot
2. Individual product selection is additive to lot selection
3. No duplicate UNSPSC codes in final selection
4. Cannot select products that don't exist in requisitions or lots

## Benefits

### For Procurement Officers
- **Flexibility**: Apply different criteria to different parts of tender
- **Efficiency**: Select entire lots instead of individual products
- **Clarity**: Clear visual indication of what's being evaluated
- **Control**: Granular control when needed, simple control when not

### For Evaluators
- **Clear Scope**: Know exactly which products each criterion applies to
- **Consistent Evaluation**: Proper criteria applied to right products
- **Reduced Errors**: Less chance of applying wrong criteria
- **Better Documentation**: Clear audit trail of evaluation scope

### For Vendors
- **Transparency**: Understand which requirements apply to which products
- **Better Preparation**: Can prepare appropriate documentation per product/lot
- **Reduced Confusion**: Clear understanding of evaluation scope
- **Fair Competition**: Criteria applied consistently and transparently

## Integration Points

### With Requisition & Lots Management
- Pulls available lots from lots management section
- Displays product counts per lot
- Shows requisition line UNSPSC codes
- Indicates product source (lot vs requisition)

### With Tender Authoring
- Criteria saved with lot/product associations
- Evaluation forms generated based on scope
- Vendor sees only applicable criteria per product
- Scoring systems respect scope boundaries

### With Evaluation Module (Future)
- Evaluation team sees correct criteria per product
- Scoring forms auto-generated based on scope
- Results aggregated properly
- Reports show criteria application clearly

## Backward Compatibility

### Existing Tenders
- Tenders without lots: Uses UNSPSC code selection only
- Empty lots array treated as product-level selection
- No breaking changes to existing data structure
- Gradual migration supported

### Default Behavior
- New criterion defaults to "Entire Tender"
- Safe default for simple tenders
- Can be changed to specific as needed
- No mandatory lot selection

## Future Enhancements

### Potential Additions
1. **Bulk Operations**: Apply same criterion to multiple lots at once
2. **Templates**: Save common criterion configurations
3. **Copy/Paste**: Copy criteria between lots
4. **Inheritance**: Child lots inherit parent lot criteria
5. **Validation Rules**: Custom validation per lot/product
6. **Weighting**: Different weights for different lots
7. **Conditional Criteria**: Show/hide based on other selections
8. **Visual Lot Tree**: Hierarchical lot visualization
9. **Drag-Drop**: Drag products between lot selections
10. **Smart Suggestions**: AI-powered criterion recommendations

## Accessibility

### Keyboard Navigation
- Full keyboard support for all selections
- Tab navigation between sections
- Ctrl/Cmd + Click for multi-select
- Enter to activate radio buttons

### Screen Reader Support
- Descriptive labels for all controls
- Selection counts announced
- Help text read by screen readers
- Error messages clearly announced

### Visual Design
- High contrast colors
- Clear focus indicators
- Large clickable areas
- Consistent spacing

## Testing Checklist

### Functional Tests
- ✅ Select "Entire Tender" - applies to all
- ✅ Select specific lot - includes all products
- ✅ Select multiple lots - merges products
- ✅ Select individual products - correct selection
- ✅ Combine lots and products - no duplicates
- ✅ Change from entire to specific - retains selection
- ✅ Change from specific to entire - clears selection
- ✅ Remove criterion - data cleaned up

### Edge Cases
- ✅ No lots exist - shows product selection only
- ✅ No products exist - shows warning
- ✅ Empty lot - shows in dropdown with (0 products)
- ✅ Product in multiple lots - shows all locations
- ✅ Remove lot after selection - updates criterion
- ✅ Remove product after selection - updates criterion

### UI/UX Tests
- ✅ Selection summary updates in real-time
- ✅ Help text is clear and helpful
- ✅ Dropdowns are properly sized
- ✅ Multi-select works correctly
- ✅ Visual feedback on hover/select
- ✅ Mobile/tablet responsive
- ✅ Keyboard navigation works

## Best Practices

### When to Use Each Option

#### Use "Entire Tender" for:
- Vendor eligibility requirements
- General qualification criteria
- Common compliance requirements
- Universal technical standards
- Basic submission requirements

#### Use "Specific Lots" for:
- Lot-level technical requirements
- Category-specific qualifications
- Grouped product requirements
- Delivery requirements by lot
- Warranty terms per lot

#### Use "Specific Products" for:
- Product-specific certifications
- High-value item requirements
- Specialized technical specs
- Unique compliance needs
- Individual product testing

#### Use "Hybrid Selection" for:
- Complex multi-lot tenders
- Mixed requirement scenarios
- Graduated requirements
- Exception handling
- Flexible evaluation needs

## Status

✅ **Implemented and Ready for Testing**

All features are complete and integrated into the evaluation criteria section without breaking existing functionality.

