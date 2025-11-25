# Evaluation Criteria - Lot/Product Selection Visual Guide

## Component Layout

### Single Criterion Card - Entire Tender Selected

```
┌───────────────────────────────────────────────────────────────┐
│ Criterion 1                                            [✖]    │
├───────────────────────────────────────────────────────────────┤
│                                                                │
│ Type:                                                          │
│ [Eligibility and formal criteria                     ▼]       │
│                                                                │
│ Evaluation Method:                                             │
│ [Pass/Fail                                            ▼]       │
│                                                                │
│ Description: *                                                 │
│ [Vendor must have valid business registration_________]       │
│ [________________________________________________]            │
│                                                                │
│ Applies To: *                                                  │
│ ● Entire Tender                                               │
│ ○ Specific Lot(s) or Product(s)                              │
│                                                                │
└───────────────────────────────────────────────────────────────┘
```

### Single Criterion Card - Lot Selection Expanded

```
┌───────────────────────────────────────────────────────────────┐
│ Criterion 2                                            [✖]    │
├───────────────────────────────────────────────────────────────┤
│                                                                │
│ Type:                                                          │
│ [Technical criteria                               ▼]          │
│                                                                │
│ Evaluation Method:                                             │
│ [Scoring (0-100)                                  ▼]          │
│                                                                │
│ Description: *                                                 │
│ [ISO 9001 certification required for manufacturing___]        │
│ [________________________________________________]            │
│                                                                │
│ Applies To: *                                                  │
│ ○ Entire Tender                                               │
│ ● Specific Lot(s) or Product(s)                              │
│                                                                │
│ ┌─────────────────────────────────────────────────────────┐  │
│ │ 🎯 Applies To Selection                                  │  │
│ ├─────────────────────────────────────────────────────────┤  │
│ │                                                          │  │
│ │ SELECT BY LOT                                            │  │
│ │ ┌────────────────────────────────────────────────────┐  │  │
│ │ │ Lot 1 - Medical Equipment (15 products)        │  │  │  │
│ │ │ Lot 2 - Surgical Instruments (8 products)      │  │  │  │
│ │ │ Lot 3 - Lab Equipment (12 products)            │  │  │  │
│ │ │                                                    │  │  │
│ │ └────────────────────────────────────────────────────┘  │  │
│ │ 💡 Hold Ctrl/Cmd to select multiple lots                 │  │
│ │                                                          │  │
│ │ OR SELECT INDIVIDUAL PRODUCTS (UNSPSC CODES)             │  │
│ │ ┌────────────────────────────────────────────────────┐  │  │
│ │ │ 42182000 - Medical diagnostic equipment [Lot 1] │  │  │  │
│ │ │ 42192000 - Surgical instruments [Lot 2]        │  │  │  │
│ │ │ 42271500 - Lab analyzers [Lot 3]               │  │  │  │
│ │ │ 44121700 - Writing paper [From requisition]    │  │  │  │
│ │ │                                                    │  │  │
│ │ └────────────────────────────────────────────────────┘  │  │
│ │ 💡 Hold Ctrl/Cmd to select multiple products             │  │
│ │                                                          │  │
│ │ ┌────────────────────────────────────────────────────┐  │  │
│ │ │ 📊 CURRENT SELECTION:                              │  │  │
│ │ │ ✓ 2 lot(s) selected                                │  │  │
│ │ │ ✓ 23 product(s) will be evaluated                  │  │  │
│ │ └────────────────────────────────────────────────────┘  │  │
│ └─────────────────────────────────────────────────────────┘  │
│                                                                │
└───────────────────────────────────────────────────────────────┘
```

### Empty State - No Lots or Products

```
┌───────────────────────────────────────────────────────────────┐
│ Criterion 3                                            [✖]    │
├───────────────────────────────────────────────────────────────┤
│                                                                │
│ Applies To: *                                                  │
│ ○ Entire Tender                                               │
│ ● Specific Lot(s) or Product(s)                              │
│                                                                │
│ ┌─────────────────────────────────────────────────────────┐  │
│ │ ⚠️ No lots or products available                         │  │
│ │                                                          │  │
│ │ Please add requisition lines or create lots with        │  │
│ │ products first.                                          │  │
│ │                                                          │  │
│ │ [Go to Requisition & Lots →]                            │  │
│ └─────────────────────────────────────────────────────────┘  │
│                                                                │
└───────────────────────────────────────────────────────────────┘
```

## Complete Evaluation Criteria Section

```
┌─────────────────────────────────────────────────────────────────┐
│ 📊 Evaluation                                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│ ╔═══════════════════════════════════════════════════════════╗  │
│ ║ ℹ️ Define evaluation criteria that will be used to       ║  │
│ ║    assess supplier submissions                             ║  │
│ ╚═══════════════════════════════════════════════════════════╝  │
│                                                                  │
│                                      [+ Add Evaluation Criterion]│
│                                                                  │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │ Criterion 1: Business Registration              [✖]         ││
│ │ Type: Eligibility | Method: Pass/Fail                       ││
│ │ ● Entire Tender                                             ││
│ └─────────────────────────────────────────────────────────────┘│
│                                                                  │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │ Criterion 2: ISO 9001 Certification              [✖]        ││
│ │ Type: Qualification | Method: Pass/Fail                     ││
│ │ ○ Specific: Lot 1, Lot 2 (23 products)                     ││
│ └─────────────────────────────────────────────────────────────┘│
│                                                                  │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │ Criterion 3: Technical Specifications            [✖]        ││
│ │ Type: Technical | Method: Scoring (0-100)                   ││
│ │ ○ Specific: 5 individual products                           ││
│ └─────────────────────────────────────────────────────────────┘│
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Selection Scenarios

### Scenario 1: Universal Requirement

```
Criterion: "Valid Business Registration"

Applies To: ● Entire Tender
            ○ Specific Lot(s) or Product(s)

Result: All vendors must provide this regardless of lot or product.
```

### Scenario 2: Lot-Level Requirement

```
Criterion: "ISO 13485 Medical Device Certification"

Applies To: ○ Entire Tender
            ● Specific Lot(s) or Product(s)

Selected Lots:
  ✓ Lot 1 - Medical Equipment (15 products)
  ✓ Lot 2 - Surgical Instruments (8 products)

Result: 23 products from 2 lots require this certification.
```

### Scenario 3: Product-Specific Requirement

```
Criterion: "Energy Star Certification"

Applies To: ○ Entire Tender
            ● Specific Lot(s) or Product(s)

Selected Products:
  ✓ 43211500 - Desktop computers [In: Lot 3]
  ✓ 43211600 - Servers [In: Lot 3]
  ✓ 43211700 - Laptops [In: Lot 3]

Result: 3 specific IT products require Energy Star.
```

### Scenario 4: Mixed Selection

```
Criterion: "Quality Management System"

Applies To: ○ Entire Tender
            ● Specific Lot(s) or Product(s)

Selected:
  Lots:
    ✓ Lot 1 - Medical Equipment (15 products)
  
  Individual Products:
    ✓ 43211600 - Servers [In: Lot 3]
    ✓ 44121700 - Security paper [From requisition]

Result: 17 products total (15 from lot + 2 individual).
```

## Interactive States

### State 1: Default (Entire Tender)
```
● Entire Tender
○ Specific Lot(s) or Product(s)

[No additional options shown]
```

### State 2: Specific Selected (No Lots Exist)
```
○ Entire Tender
● Specific Lot(s) or Product(s)

[Only product selection shown]

SELECT PRODUCTS (UNSPSC CODES)
[Product dropdown appears]
```

### State 3: Specific Selected (Lots Exist)
```
○ Entire Tender
● Specific Lot(s) or Product(s)

SELECT BY LOT
[Lot dropdown appears]

OR SELECT INDIVIDUAL PRODUCTS
[Product dropdown appears]
```

### State 4: Lots Selected
```
SELECT BY LOT
[Lot 1 - Medical Equipment (15 products)    ] ← Selected (blue highlight)
[Lot 2 - Surgical Instruments (8 products)  ]
[Lot 3 - Lab Equipment (12 products)        ]

📊 CURRENT SELECTION:
✓ 1 lot(s) selected
✓ 15 product(s) will be evaluated
```

### State 5: Products Selected
```
SELECT INDIVIDUAL PRODUCTS
[42182000 - Medical diagnostic [Lot 1]      ] ← Selected (blue highlight)
[42192000 - Surgical instruments [Lot 2]    ] ← Selected (blue highlight)
[42271500 - Lab analyzers [Lot 3]           ]

📊 CURRENT SELECTION:
✓ 2 product(s) will be evaluated
```

### State 6: Both Lots and Products Selected
```
SELECT BY LOT
[Lot 1 - Medical Equipment (15 products)    ] ← Selected
[Lot 2 - Surgical Instruments (8 products)  ] ← Selected

SELECT INDIVIDUAL PRODUCTS
[43211600 - Servers [Lot 3]                 ] ← Selected
[44121700 - Security paper [From req]       ] ← Selected

📊 CURRENT SELECTION:
✓ 2 lot(s) selected
✓ 25 product(s) will be evaluated
```

## Color Coding

### Status Colors
- 🔵 **Blue** - Selected items (lots/products)
- 🟢 **Green** - Valid selection, ready to save
- 🟡 **Yellow/Orange** - Warning (no lots/products available)
- 🔴 **Red** - Error (validation failed)

### Section Colors
- **Light Gray Background** - Applies To selection area
- **Blue Tint** - Information panels
- **White** - Dropdown areas
- **Light Blue** - Selection summary

## Keyboard Shortcuts

### Multi-Select
- **Ctrl + Click** (Windows/Linux) - Select multiple items
- **Cmd + Click** (Mac) - Select multiple items
- **Shift + Click** - Select range of items

### Navigation
- **Tab** - Move between fields
- **Shift + Tab** - Move backwards
- **Space** - Toggle radio buttons
- **Enter** - Activate focused button
- **Esc** - Close dropdowns/dialogs

## Responsive Behavior

### Desktop (> 1280px)
```
┌──────────────────────┬──────────────────────┐
│ SELECT BY LOT        │ SELECT PRODUCTS      │
│                      │                      │
│ [Dropdown full]      │ [Dropdown full]      │
│                      │                      │
│ [Summary below]      │                      │
└──────────────────────┴──────────────────────┘
```

### Tablet (768px - 1280px)
```
┌────────────────────────────────────────────┐
│ SELECT BY LOT                              │
│ [Dropdown full width]                      │
│                                            │
│ SELECT PRODUCTS                            │
│ [Dropdown full width]                      │
│                                            │
│ [Summary full width]                       │
└────────────────────────────────────────────┘
```

### Mobile (< 768px)
```
┌────────────────────────────┐
│ SELECT BY LOT              │
│ [Dropdown]                 │
│                            │
│ SELECT PRODUCTS            │
│ [Dropdown]                 │
│                            │
│ [Summary]                  │
└────────────────────────────┘
```

## Data Flow Diagram

```
User Action                  System Process              Result
───────────                  ──────────────              ──────

Select "Entire"      →       appliesTo = 'entire'  →    Applies to all
                             lots = []
                             unspscCodes = []

Select "Specific"    →       appliesTo = 'specific' →   Show options
                             Show lot & product
                             selection dropdowns

Select Lot(s)        →       lots = ['lot_1', ...]  →   Auto-populate
                             Get all UNSPSC codes         unspscCodes
                             from selected lots

Select Product(s)    →       unspscCodes = [...]    →   Add to selection
                             Add to existing codes
                             (no duplicates)

Remove Selection     →       Update arrays          →    Update summary
                             Recalculate totals           Refresh display
```

## Validation States

### Valid - Entire Tender
```
✓ Applies To: Entire Tender
✓ All products will be evaluated
✓ Ready to save
```

### Valid - Specific with Selection
```
✓ Applies To: Specific Lot(s) or Product(s)
✓ 2 lots selected
✓ 25 products will be evaluated
✓ Ready to save
```

### Invalid - Specific without Selection
```
✗ Applies To: Specific Lot(s) or Product(s)
✗ No lots or products selected
✗ Please select at least one lot or product
```

### Warning - No Lots/Products Available
```
⚠ No lots or products available
⚠ Add requisition lines or create lots first
⚠ Cannot use "Specific" option yet
```

## User Journey Map

### Journey 1: Simple Universal Criterion
```
1. Click "+ Add Evaluation Criterion"
2. Select type and method
3. Enter description
4. Select "Entire Tender"
5. Save
   
Time: ~2 minutes
Complexity: Low
```

### Journey 2: Lot-Based Criterion
```
1. Click "+ Add Evaluation Criterion"
2. Select type and method
3. Enter description
4. Select "Specific Lot(s) or Product(s)"
5. View lot dropdown
6. Select desired lots (Ctrl+Click for multiple)
7. Review selection summary
8. Save

Time: ~3 minutes
Complexity: Medium
```

### Journey 3: Product-Specific Criterion
```
1. Click "+ Add Evaluation Criterion"
2. Select type and method
3. Enter description
4. Select "Specific Lot(s) or Product(s)"
5. Scroll to product dropdown
6. Find specific UNSPSC codes
7. Select products (Ctrl+Click)
8. Review selection summary
9. Save

Time: ~4 minutes
Complexity: Medium-High
```

### Journey 4: Complex Hybrid Selection
```
1. Click "+ Add Evaluation Criterion"
2. Select type and method
3. Enter description
4. Select "Specific Lot(s) or Product(s)"
5. Select entire lots in lot dropdown
6. Additionally select specific products
7. Review combined selection summary
8. Adjust if needed
9. Save

Time: ~5 minutes
Complexity: High
```

## Best Practices Visualization

### ✅ DO: Use Entire Tender for Universal Requirements
```
Vendor Registration      → ● Entire Tender
Tax Compliance          → ● Entire Tender
Insurance Requirements  → ● Entire Tender
```

### ✅ DO: Use Lot Selection for Category Requirements
```
Medical Certification   → ○ Specific → Select Lot 1 (Medical)
IT Standards           → ○ Specific → Select Lot 3 (IT)
Food Safety           → ○ Specific → Select Lot 2 (Food)
```

### ✅ DO: Use Product Selection for Specific Items
```
FDA Approval          → ○ Specific → Select high-risk devices
Energy Rating         → ○ Specific → Select electronic items
Custom Certification  → ○ Specific → Select unique products
```

### ❌ DON'T: Create Separate Criteria for Each Product When Lot Works
```
Bad:  ISO Cert - Product 1
      ISO Cert - Product 2
      ISO Cert - Product 3
      ...15 separate criteria

Good: ISO Cert - Lot 1 (15 products)
```

### ❌ DON'T: Use "Entire Tender" When Only Some Products Need It
```
Bad:  "Medical Device Certification" → Entire Tender
      (But only 2 products are medical devices!)

Good: "Medical Device Certification" → Specific Products
      Select only the actual medical device products
```

