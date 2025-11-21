# Mock-up Updates Summary

## Overview

This document summarizes all changes made to `tender-create.html` (tender authoring mode) and `template-builder-workspace.html` (admin mode) based on the latest user requirements.

---

## ✅ Changes to SCOPE & REQUIREMENTS Block

### 1. **Product Requirements & Specifications** Section

#### **Changes Made:**

- **Title Updated:** Changed from "Product Requirements & Specifications (per UNSPSC Code)" to "Product Requirements & Specifications"

- **Removed:** "Supplier Fields (What Supplier Will Provide)" section has been completely removed

- **Updated:** "Requirements (What I Want to Buy)" changed to just "Requirements"

- **Mutually Exclusive Options:** Users must now choose EITHER:
  - **Attributes** - Structured name-value pairs (e.g., "Printing technology: Laser")
  - **Description Field** - Free-text rich textarea (ideal for service descriptions)
  
- **New Feature:** Spreadsheet Import/Export buttons added at the top:
  - 📥 Export to Spreadsheet
  - 📤 Upload from Spreadsheet
  - Helper text: "Use spreadsheet templates to bulk manage requirements"

#### **User Flow:**

1. For each UNSPSC code, user sees two prominent buttons:
   - **"📋 Add Attributes"** - For structured requirements
   - **"📝 Add Description Field"** - For free-text descriptions

2. Once chosen:
   - **Attributes Mode**: Can add multiple attributes with name-value pairs
   - **Description Mode**: Shows a large textarea for detailed description
   - Users can switch modes (with confirmation) or clear and start over

#### **Updated Data Structure:**

```javascript
productRequirements = {
  unspscCode: {
    requirementType: 'attributes' | 'description',
    attributes: [{ name, value }],
    description: ''
  }
}
```

#### **New Functions:**

- `addRequirementAttribute(unspscCode)` - Switches to attributes mode
- `addDescriptionField(unspscCode)` - Switches to description mode
- `updateRequirementDescription(unspscCode, newDescription)` - Updates description
- `switchToAttributes(unspscCode)` - Converts from description to attributes
- `clearRequirements(unspscCode)` - Clears all requirements for UNSPSC
- `exportProductRequirementsToSpreadsheet()` - Export functionality placeholder
- `uploadProductRequirementsSpreadsheet()` - Upload functionality placeholder

---

### 2. **Tender Author Configuration** Section

#### **Changes Made:**

- **Removed:** "1. UNSPSC Configuration" section (completely removed)
- **Removed:** "4. General Parameters" section (completely removed)
- **Renumbered:** Sections are now:
  - **1. Service Descriptions** (was #2)
  - **2. Delivery Configuration** (was #3)

#### **Updated: Delivery Configuration**

- **Allowed Incoterms** changed from single-select dropdown to **multi-select**
- Users can now Ctrl/Cmd + Click to select multiple Incoterms
- Help text added: "Hold Ctrl/Cmd to select multiple Incoterms"

#### **Updated Data Structure:**

```javascript
tenderAuthorConfig = {
  serviceDescriptions: [{ name, description }],
  deliveryConfig: {
    deliveryTime: '',
    deliveryPlace: '',
    allowedIncoterms: [], // Now an array
    consigneeDetails: '',
  },
}
```

#### **Removed Functions:**

- `addUNSPSCAttribute()`
- `addAttributeToUNSPSC(code)`
- `removeUNSPSCAttribute(code, idx)`
- `addCustomParameter()`

#### **New Functions:**

- `updateAllowedIncoterms(selectElement)` - Updates array from multi-select

---

## ✅ Changes to SUBMISSION REQUIREMENTS Block

### **Bid Response Requirements** Section (formerly "Supplier Response Structure")

#### **Changes Made:**

- **Title Updated:** "Supplier Response Structure" → **"Bid Response Requirements"**

#### **B. Per UNSPSC** - New Fields Added:

Added three new fields after "Delivery point(s)":
- **Model** - Text input
- **Manufacturer** - Text input
- **Country of origin** - Country dropdown

#### **E. Delivery Requirements** - Completely Restructured:

**Old Structure (varied field types):**
- Delivery time → Yes/No + Text input
- Delivery place & Incoterms rules → Yes/No + Text input
- Consignee details → Yes/No + Text area
- UNOPS Right to vary requirements → Yes/No + Text area

**New Structure (consistent per-requirement format):**
- **Is quotation compliant?** → Yes/No radio
- **Details** → Text area

This now follows the same pattern as **D. Per Service Line** for consistency.

---

## 📁 Files Modified

### **tender-create.html** (Tender Authoring Mode)

#### **Data Structures:**
- Updated `productRequirements` structure
- Updated `tenderAuthorConfig` structure
- Removed `generalParameters` and `unspscAttributes`

#### **Functions Added:**
- Product requirements: 5 new functions
- Tender author config: `updateAllowedIncoterms()`
- Spreadsheet import/export: 2 placeholder functions

#### **Functions Removed:**
- 4 UNSPSC-related functions
- 1 custom parameter function

#### **Sections Updated:**
1. Product Requirements & Specifications section (complete rewrite)
2. Tender Author Configuration section (simplified)
3. Bid Response Requirements section (field additions)

---

### **template-builder-workspace.html** (Admin Mode)

#### **Block Structure Updates:**

**Scope & Requirements block:**
- Added "Product Requirements & Specifications" subsection
- Updated "Tender Author Configuration" fields
- Removed UNSPSC and General Parameters references

**Submission Requirements block:**
- Renamed subsection to "Bid Response Requirements"
- Updated field list

---

## 🎨 Visual Design Updates

### Product Requirements Section

- **Choice Screen:** Large, prominent buttons with icons
- **Attributes Mode:** Clean name-value pair inputs
- **Description Mode:** Large textarea (min-height: 200px) with formatting support
- **Actions:** Clear buttons for switching modes or starting over
- **Spreadsheet Tools:** Subtle secondary buttons in a light gray container

### Tender Author Configuration

- **Cleaner Layout:** Only 2 numbered sections instead of 4
- **Multi-select Incoterms:** Better visual indication of multiple selections
- **Consistent Spacing:** Maintained UNOPS design system

### Bid Response Requirements

- **Color-Coded Groups:** Maintained visual hierarchy
  - Per Bid → Blue
  - Per UNSPSC → Teal (now with 3 additional fields)
  - Per Attribute → Green
  - Per Service Line → Orange
  - Per Delivery Requirement → Red (simplified structure)

---

## 🧪 Testing Checklist

### Product Requirements & Specifications

- [ ] Spreadsheet export button displays and shows alert
- [ ] Spreadsheet upload button displays and shows alert
- [ ] Initial choice screen shows both "Add Attributes" and "Add Description Field" buttons
- [ ] Clicking "Add Attributes" creates attribute mode
- [ ] Clicking "Add Description Field" creates description mode
- [ ] In attribute mode, can add multiple attributes
- [ ] In attribute mode, can remove attributes
- [ ] In description mode, textarea is large and editable
- [ ] "Switch to Attributes" button appears in description mode
- [ ] Confirmation dialog appears when switching modes
- [ ] "Clear & Start Over" works in both modes
- [ ] Data persists when navigating away and back

### Tender Author Configuration

- [ ] Only 2 sections visible (Service Descriptions, Delivery Configuration)
- [ ] Can add service lines
- [ ] Can update service line name and description
- [ ] Can remove service lines
- [ ] Delivery time field works
- [ ] Delivery place field works
- [ ] Incoterms multi-select allows multiple selections (Ctrl/Cmd + Click)
- [ ] Consignee details textarea works
- [ ] Selected Incoterms persist when re-rendering

### Bid Response Requirements

- [ ] Section title displays as "Bid Response Requirements"
- [ ] All 5 groups (A-E) display correctly
- [ ] Per UNSPSC section shows new fields (Model, Manufacturer, Country of origin)
- [ ] Per Delivery Requirement shows simplified structure (Is compliant? + Details)
- [ ] Color coding is correct for all sections
- [ ] No console errors

---

## 📊 Before & After Comparison

### Product Requirements

| Aspect | Before | After |
|--------|--------|-------|
| **Title** | "...per UNSPSC Code" | "Product Requirements & Specifications" |
| **Structure** | Requirements + Supplier Fields | Requirements only (mutually exclusive) |
| **Requirements Type** | Only attributes | Attributes OR Description |
| **Spreadsheet Tools** | None | Export & Upload buttons |
| **Supplier Fields** | Separate section | Removed |

### Tender Author Configuration

| Aspect | Before | After |
|--------|--------|-------|
| **Sections** | 4 sections | 2 sections |
| **UNSPSC Config** | Full section | Removed |
| **General Parameters** | Full section | Removed |
| **Allowed Incoterms** | Single-select (Delivery Config) | Multi-select |
| **Allowed Currencies** | Multi-select (General Parameters) | Removed |

### Bid Response Requirements

| Aspect | Before | After |
|--------|--------|-------|
| **Title** | "Supplier Response Structure" | "Bid Response Requirements" |
| **Per UNSPSC Fields** | 7 fields | 10 fields (+Model, +Manufacturer, +Country) |
| **Delivery Requirements** | 4 varied fields | 2 standardized fields |
| **Pattern Consistency** | Mixed | Consistent with Per Service Line |

---

## 🔄 Migration Notes

### If Upgrading from Previous Version:

1. **Data Migration for Product Requirements:**
   ```javascript
   // Old: { attributes: [...], supplierFields: [...] }
   // New: { requirementType: 'attributes', attributes: [...], description: '' }
   ```
   - Remove `supplierFields` property
   - Add `requirementType` property
   - Add `description` property

2. **Data Migration for Tender Author Config:**
   ```javascript
   // Old: { unspscAttributes: {...}, ..., generalParameters: {...} }
   // New: { serviceDescriptions: [...], deliveryConfig: {...} }
   ```
   - Remove `unspscAttributes`
   - Remove `generalParameters`
   - Update `deliveryConfig.incoterms` from string to `allowedIncoterms` array

3. **No migration needed for Bid Response Requirements** (read-only mockup structure)

---

## 📚 Related Documentation

- See `NEW_FEATURES_SUMMARY.md` for previous feature additions
- See `REQUISITION_LOTS_INTEGRATION.md` for Requisition Lines & Lots functionality
- See `SYNC_GUIDE.md` for keeping both mockups synchronized

---

## 🎯 Key Benefits of Changes

### Improved User Experience
- **Clearer choices:** Mutually exclusive options prevent confusion
- **Bulk operations:** Spreadsheet import/export for efficiency
- **Simpler configuration:** Removed unnecessary complexity
- **Consistent patterns:** Delivery Requirements follow established patterns

### Better Data Structure
- **Type safety:** Explicit `requirementType` field
- **Flexibility:** Support both structured and unstructured requirements
- **Array consistency:** All multi-selects use arrays

### Maintainability
- **Fewer functions:** Removed 5 functions, added 7 (net +2 but clearer purpose)
- **Clear separation:** Requirements vs Configuration vs Response Structure
- **Modular updates:** Changes isolated to specific sections

---

## 💡 Future Enhancements

### Planned Features

1. **Spreadsheet Import/Export Implementation**
   - Generate Excel templates with UNSPSC codes
   - Parse uploaded spreadsheets to populate requirements
   - Validation and error handling

2. **Rich Text Editor for Descriptions**
   - Replace textarea with proper WYSIWYG editor
   - Support formatting (bold, italic, lists, etc.)
   - HTML export for tender documents

3. **Attribute Library**
   - Pre-defined common attributes by UNSPSC category
   - Auto-suggest values based on history
   - Copy attributes between UNSPSC codes

4. **Multi-Incoterm Support in UI**
   - Better visual indication of selected items
   - Quick select/deselect all
   - Grouped by category (transport mode)

5. **Delivery Requirement Templates**
   - Pre-defined sets of delivery requirements
   - Copy from previous tenders
   - Industry-specific templates

---

## ⚠️ Breaking Changes

### Functions No Longer Available:
- `addUNSPSCAttribute()`
- `addAttributeToUNSPSC(code)`
- `removeUNSPSCAttribute(code, idx)`
- `addSupplierField(unspscCode)`
- `updateSupplierFieldName(unspscCode, index, newName)`
- `updateSupplierFieldValue(unspscCode, index, newValue)`
- `removeSupplierField(unspscCode, index)`
- `addCustomParameter()`

### Data Structure Changes:
- `productRequirements[code].supplierFields` removed
- `tenderAuthorConfig.unspscAttributes` removed
- `tenderAuthorConfig.generalParameters` removed
- `tenderAuthorConfig.deliveryConfig.incoterms` → `allowedIncoterms` (string → array)

---

## 📞 Support & Questions

For questions about implementation:
1. Check this document for structural changes
2. Review function signatures for updated parameters
3. Test in browser to verify behavior
4. Check browser console for any JavaScript errors

---

## ✨ Summary

All requested changes have been successfully implemented:

✅ Product Requirements simplified with mutually exclusive options  
✅ Spreadsheet import/export placeholders added  
✅ UNSPSC Configuration removed from Tender Author Config  
✅ General Parameters removed from Tender Author Config  
✅ Allowed Incoterms changed to multi-select  
✅ "Supplier Response Structure" renamed to "Bid Response Requirements"  
✅ Model, Manufacturer, Country of origin added to Per UNSPSC  
✅ Delivery Requirements simplified to match Per Service Line pattern  

Both mockup files are now updated and ready for testing! 🎉

