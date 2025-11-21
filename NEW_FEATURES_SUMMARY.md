# New Features Summary

## Overview

Two new major sections have been added to both mockup files:

1. **Tender Author Configuration** - Added to SCOPE & REQUIREMENTS block
2. **Supplier Response Structure** - Added to SUBMISSION REQUIREMENTS block

## 1. Tender Author Configuration Section

**Location:** SCOPE & REQUIREMENTS block

### Purpose
Allows tender authors to configure tender-specific requirements that will structure how suppliers respond.

### Sub-sections

#### 1.1 UNSPSC Configuration
- **Purpose:** Define attributes for each UNSPSC code
- **Features:**
  - Add multiple UNSPSC codes
  - For each UNSPSC, add attributes with:
    - Attribute name
    - Value
    - Description
  - Mock repeater structure
  - Add/remove functionality

#### 1.2 Service Descriptions
- **Purpose:** Define service lines required in the tender
- **Features:**
  - List of service lines
  - Each service has:
    - Service name (required)
    - Description (optional)
  - Add/remove service lines dynamically

#### 1.3 Delivery Configuration
- **Purpose:** Specify delivery requirements
- **Fields:**
  - Delivery Time (text input)
  - Delivery Place (text input)
  - Allowed Incoterms (dropdown with options):
    - EXW - Ex Works
    - FCA - Free Carrier
    - CPT - Carriage Paid To
    - CIP - Carriage and Insurance Paid To
    - DAP - Delivered at Place
    - DPU - Delivered at Place Unloaded
    - DDP - Delivered Duty Paid
  - Consignee Details (textarea)

#### 1.4 General Parameters
- **Purpose:** Set global tender parameters
- **Fields:**
  - Allowed Currencies (multi-select):
    - USD - US Dollar
    - EUR - Euro
    - GBP - British Pound
    - CHF - Swiss Franc
    - JPY - Japanese Yen
    - LOCAL - Local Currency
  - Allowed Incoterms (multi-select):
    - Same options as Delivery Configuration
  - "+ Add Custom Parameter" button for future extensibility

### User Interactions

**tender-create.html functions:**
- `addUNSPSCAttribute()` - Add new UNSPSC code
- `addAttributeToUNSPSC(code)` - Add attribute to specific UNSPSC
- `removeUNSPSCAttribute(code, idx)` - Remove attribute
- `addServiceLine()` - Add new service line
- `updateServiceLine(idx, field, value)` - Update service line data
- `removeServiceLine(idx)` - Remove service line
- `addCustomParameter()` - Placeholder for custom parameters

## 2. Supplier Response Structure Section

**Location:** SUBMISSION REQUIREMENTS block

### Purpose
Visual mockup showing how supplier questions will be grouped and structured by answer level.

### Groups

#### A. Per Bid (answered once for entire bid)
Fields suppliers answer once per bid:
- Bidder's Total Prices [Incoterm] - Currency input
- Customs clearance costs - Currency input
- Freight cost - Currency input
- Total Price of Goods [Incoterm] - Currency input (calculated)
- Total Price of Related Services - Currency input

**Visual Design:** Blue left border (primary color)

#### B. Per UNSPSC (for each UNSPSC item)
Fields suppliers answer for each UNSPSC code:
- Unit price Incoterm - Currency input
- Total price Incoterm - Currency input (calculated)
- Country of Origin - Country dropdown
- Delivery point(s) - Text input
- Shipment Dimensions:
  - Gross weight - Number input
  - Total volume - Number input
  - Containers – Number - Number input
  - Containers – Size - Number input

**Visual Design:** Teal left border (info color)

#### C. Per Attribute (under each UNSPSC attribute)
Table structure showing fields per attribute:
- Is quotation compliant? - Yes/No radio
- Details of goods offered - Text area
- Model - Text input
- Manufacturer - Text input
- Country of origin - Country dropdown

**Visual Design:** Green left border (success color), table format

#### D. Per Service Line
Fields for each service line:
- Is quotation compliant? - Yes/No radio
- Details - Text area

**Visual Design:** Orange left border (warning color)

#### E. Delivery Requirements (per requirement)
Fields for each delivery requirement:
- Delivery time - Yes/No + Text input
- Delivery place & Incoterms rules - Yes/No + Text input
- Consignee details - Yes/No + Text area
- UNOPS Right to vary requirements - Yes/No + Text area

**Visual Design:** Red left border (danger color)

## Implementation Details

### tender-create.html

**Data Structure:**
```javascript
let tenderAuthorConfig = {
  unspscAttributes: {}, // { unspscCode: [{ name, value, description }] }
  serviceDescriptions: [], // [{ name, description }]
  deliveryConfig: {
    deliveryTime: '',
    deliveryPlace: '',
    incoterms: '',
    consigneeDetails: '',
  },
  generalParameters: {
    allowedCurrencies: [],
    allowedIncoterms: [],
  },
};
```

**New Rendering Functions:**
- `renderTenderAuthorConfigSection(block, section, sectionIndex, isFirst)` - Renders the configuration section
- `renderSupplierResponseStructureSection(block, section, sectionIndex, isFirst)` - Renders the supplier response structure mockup

**Integration:**
Added checks in `renderCurrentBlock()`:
```javascript
if (section.isTenderAuthorConfig) {
  html += renderTenderAuthorConfigSection(block, section, sectionIndex, isFirst);
  return;
}

if (section.isSupplierResponseStructure) {
  html += renderSupplierResponseStructureSection(block, section, sectionIndex, isFirst);
  return;
}
```

### template-builder-workspace.html

**Updates:**
- Added "Tender Author Configuration" subsection to Scope & Requirements block
- Added "Supplier Response Structure" subsection to Submission Requirements block
- Fields listed show what will be configured by admins

**Visual Representation:**
In template builder mode, these appear as standard field items that can be:
- Clicked to view properties
- Configured through the properties panel
- Moved/reordered within the block

## Visual Design Features

### Color Coding by Group
- **Per Bid:** Primary blue (`--primary-400`)
- **Per UNSPSC:** Info teal (`--info-400`)
- **Per Attribute:** Success green (`--success-500`)
- **Per Service Line:** Warning orange (`--warning-500`)
- **Delivery Requirements:** Danger red (`--danger-500`)

### Card Style
Each group uses:
- Left border (4px) with group color
- Rounded corners (`--radius-sm`)
- Card shadow (`--shadow-card`)
- Light background (`--surface-0`)
- Padding for comfortable spacing

### Field Display
Each field shows:
- **Bold field name** - What the field is
- **Muted field type** - Type of input (e.g., "Currency input", "Yes/No radio")

### Table Format (Per Attribute)
Special table layout for attribute-level questions:
- Two-column table
- Header row with background
- Bordered cells
- Responsive overflow handling

## Usage Instructions

### For Tender Authors (tender-create.html)

1. **Navigate to Scope & Requirements block**
2. **Open "Tender Author Configuration" section**
3. **Configure each sub-section:**
   
   **UNSPSC Configuration:**
   - Click "+ Add UNSPSC Attribute"
   - Enter UNSPSC code
   - Click "+ Add Attribute" for that UNSPSC
   - Fill in name, value, and description
   
   **Service Descriptions:**
   - Click "+ Add Service Line"
   - Enter service name and optional description
   
   **Delivery Configuration:**
   - Fill in all delivery fields
   - Select appropriate Incoterm
   
   **General Parameters:**
   - Select allowed currencies (Ctrl/Cmd + Click for multiple)
   - Select allowed Incoterms (Ctrl/Cmd + Click for multiple)

4. **View Submission Requirements structure**
   - Navigate to Submission Requirements block
   - Open "Supplier Response Structure" section
   - Review how supplier questions are grouped
   - This is read-only mockup showing the structure

### For Template Admins (template-builder-workspace.html)

1. **Drag Scope & Requirements block to canvas**
2. **See "Tender Author Configuration" subsection**
3. **Click to configure in properties panel:**
   - Enable/disable each sub-section
   - Set default values
   - Configure field requirements

4. **Drag Submission Requirements block to canvas**
5. **See "Supplier Response Structure" subsection**
6. **Configure which groups are enabled**
7. **Save template for tender authors to use**

## Future Enhancements

### Planned Features

1. **Dynamic Field Management**
   - Allow tender authors to add/remove fields from groups
   - Custom field types beyond predefined ones
   - Field validation rules

2. **Conditional Logic**
   - Show/hide fields based on other answers
   - Required field dependencies
   - Calculated fields

3. **Import/Export**
   - Export configuration as JSON
   - Import from Excel/CSV
   - Template reuse across tenders

4. **Preview Mode**
   - Preview supplier form before publishing
   - Test data entry workflow
   - Validation testing

5. **Integration**
   - Connect to actual UNSPSC database
   - Auto-populate from ERP
   - Real-time currency conversion

## Technical Notes

### CSS Variables Used
- `--primary-400`, `--primary-600` - Primary actions and headers
- `--info-400` - Informational elements
- `--success-500` - Success states and confirmations
- `--warning-500` - Warnings and important notices
- `--danger-500` - Critical items and deletions
- `--surface-*` - Background layers
- `--text-*` - Typography hierarchy
- `--spacing-*` - Consistent spacing scale

### Responsive Behavior
- Form grids use 2-column layout
- Tables have horizontal scroll on small screens
- Cards stack vertically
- Multi-select dropdowns maintain size

### Accessibility
- Semantic HTML structure
- Clear labels for all inputs
- Help text for complex fields
- Color coding supplemented with text labels
- Keyboard navigation support

## Testing Checklist

### Tender Author Configuration
- [ ] Add UNSPSC attribute
- [ ] Add multiple attributes to same UNSPSC
- [ ] Remove UNSPSC attribute
- [ ] Add service line
- [ ] Update service line name and description
- [ ] Remove service line
- [ ] Fill delivery configuration
- [ ] Select multiple currencies
- [ ] Select multiple Incoterms
- [ ] Data persists when switching blocks
- [ ] Click "Add Custom Parameter" (shows alert)

### Supplier Response Structure
- [ ] Section renders correctly
- [ ] All 5 groups display
- [ ] Color coding is visible
- [ ] Per Attribute table renders correctly
- [ ] Field types are labeled
- [ ] Info alert displays
- [ ] Section is read-only (no edit controls)

### Integration
- [ ] Sections appear in correct block
- [ ] Order matches specification
- [ ] No console errors
- [ ] Proper spacing and alignment
- [ ] Scrolling works for long content
- [ ] Data structure is maintained
- [ ] State updates trigger re-renders

## Files Modified

1. **tender-create.html**
   - Added `tenderAuthorConfig` data structure
   - Added `renderTenderAuthorConfigSection()` function
   - Added `renderSupplierResponseStructureSection()` function
   - Added helper functions for UNSPSC and service management
   - Updated `renderCurrentBlock()` to handle new sections
   - Added new sections to `templateBlocks[scope]`
   - Added new section to `templateBlocks[submission]`

2. **template-builder-workspace.html**
   - Added "Tender Author Configuration" to scope subsections
   - Added "Supplier Response Structure" to submission subsections
   - Fields appear as standard field items for configuration

## Screenshots Guide

### Tender Author Configuration Section
Should show:
- Info alert at top
- 4 collapsible/expandable cards
- Each card with distinct functionality
- Add buttons visible
- Form inputs styled consistently

### Supplier Response Structure Section
Should show:
- Info alert explaining mockup
- 5 colored groups stacked vertically
- Each group clearly labeled
- Fields with type indicators
- Table format for "Per Attribute" group

## Related Documentation

- See `REQUISITION_LOTS_INTEGRATION.md` for similar pattern implementation
- See `SYNC_GUIDE.md` for keeping both files synchronized
- Refer to design system documentation for color meanings
- Check UNOPS brand guidelines for terminology

## Support

For questions or issues:
1. Check if data structure matches specification
2. Verify all helper functions are globally accessible
3. Confirm CSS variables are defined
4. Test in multiple browsers
5. Review console for JavaScript errors

