# General Information Block - Enhanced Fields Summary

## Overview
This document summarizes the comprehensive field enhancements made to the "General Information" block in `tender-create.html`, including conditional visibility logic and new field types.

---

## ✅ New Fields Added

### **1. Title**
- **Field Type:** Text
- **Required:** Yes
- **Purpose:** Main title of the tender/sourcing process
- **Visibility:** Always visible

---

### **2. Sourcing or Solicitation**
- **Field Type:** Dropdown
- **Required:** Yes
- **Options:** 
  - Sourcing
  - Solicitation
- **Purpose:** Determines the type of procurement process
- **Visibility:** Always visible
- **Triggers:** Shows/hides multiple dependent fields

---

### **3. Sourcing Method**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - RFI (Request for Information)
  - EOI (Expression of Interest)
  - PQ (Pre-Qualification)
  - Ad-hoc shortlist
- **Purpose:** Defines the specific sourcing method
- **Visibility:** ✨ **Visible only if "Sourcing" is selected**

---

### **4. Modality of Bid Receipt**
- **Field Type:** Dropdown
- **Required:** Yes
- **Options:**
  - Online Only
  - Email Submission
  - Physical Submission
- **Purpose:** Defines how bids will be received
- **Visibility:** Always visible
- **Triggers:** Shows/hides Waiver field

---

### **5. Waiver for Bid Receipt Modality**
- **Field Type:** Text
- **Required:** No
- **Placeholder:** "Provide waiver link"
- **Purpose:** Link to waiver document for non-online submissions
- **Visibility:** ✨ **Visible only if "Email Submission" OR "Physical Submission" is selected**

---

### **6. EPP (Emergency Procurement Procedures)**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - Yes
  - No
- **Purpose:** Indicates if Emergency Procurement Procedures apply
- **Visibility:** Always visible
- **Triggers:** Shows/hides 3 EPP-related fields

---

### **7. EPP Authorization (Jira Link)**
- **Field Type:** Text
- **Required:** No
- **Placeholder:** "Enter Jira link"
- **Purpose:** Link to EPP authorization in Jira
- **Visibility:** ✨ **Visible only if EPP = Yes**

---

### **8. EPP Authorization End Date**
- **Field Type:** Date
- **Required:** No
- **Purpose:** Expiration date of EPP authorization
- **Visibility:** ✨ **Visible only if EPP = Yes**

---

### **9. Scope of EPP Approval**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - Project
  - Org Unit
  - Specific procurement process
  - Other
- **Purpose:** Defines the scope of EPP approval
- **Visibility:** ✨ **Visible only if EPP = Yes**

---

### **10. Medicine / Medical Equipment**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - Yes
  - No
- **Purpose:** Indicates if procurement involves medicine/medical equipment
- **Visibility:** Always visible
- **Triggers:** Shows/hides Policy Exception field

---

### **11. Policy Exception for Medicine/Medical Equipment Procurement (Annex 2 Jira Link)**
- **Field Type:** Text
- **Required:** No
- **Placeholder:** "Enter Jira link"
- **Purpose:** Link to policy exception documentation
- **Visibility:** ✨ **Visible only if Medicine / Medical Equipment = Yes**

---

### **12. Process Type**
- **Field Type:** Checkbox Group (Multi-select)
- **Required:** No
- **Options:**
  - Standard solicitation
  - Solicit offers against LTA/BPA
  - Secondary bidding against LTA/BPA
  - Direct contracting / Sole sourcing
- **Purpose:** Defines the procurement process type(s)
- **Visibility:** ✨ **Visible only if "Solicitation" is selected**
- **Triggers:** Shows/hides Contract Amendment and Linked Process fields

---

### **13. Contract Amendment**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - Yes
  - No
- **Purpose:** Indicates if this is a contract amendment
- **Visibility:** ✨ **Visible only if "Direct contracting / Sole sourcing" is selected in Process Type**

---

### **14. Solicitation Method**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - RFQ (Request for Quotation)
  - ITB (Invitation to Bid)
  - RFP (Request for Proposal)
  - e-reserve auctions
- **Purpose:** Defines the specific solicitation method
- **Visibility:** ✨ **Visible only if "Solicitation" is selected**

---

### **15. Linked Process**
- **Field Type:** Search (with icon)
- **Required:** No
- **Placeholder:** "Search by Event ID"
- **Purpose:** Links to another procurement process/event
- **Visibility:** ✨ **Visible if ANY of the following:**
  - "Direct contracting / Sole sourcing" is selected in Process Type
  - "Sourcing or Solicitation" has a value
  - "Solicit offers against LTA/BPA" is selected
  - "Contract Amendment" is selected

---

### **16. UN Collaboration**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - Yes
  - No
- **Purpose:** Indicates if this involves UN collaboration
- **Visibility:** Always visible
- **Triggers:** Shows/hides 2 UN-related fields

---

### **17. UN Collaboration Type**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - Joint tendering process with other UN organizations
  - Secondary bidding using other UN agency's LTA
  - Re-use of solicitation results of another UN agency
- **Purpose:** Defines the type of UN collaboration
- **Visibility:** ✨ **Visible only if UN Collaboration = Yes**

---

### **18. UN Agencies**
- **Field Type:** Multi-select Dropdown
- **Required:** No
- **Options:**
  - UNDP
  - UNICEF
  - UNHCR
  - WFP
  - WHO
  - FAO
  - ILO
  - UNESCO
  - UNIDO
- **Purpose:** Select collaborating UN agencies
- **Visibility:** ✨ **Visible only if UN Collaboration = Yes**

---

### **19. Organizational Unit**
- **Field Type:** Search (with icon)
- **Required:** No
- **Placeholder:** "Search from oneUNOPS"
- **Purpose:** Search and select organizational unit from oneUNOPS
- **Visibility:** Always visible

---

### **20. Is this a Lease**
- **Field Type:** Dropdown
- **Required:** No
- **Options:**
  - Yes
  - No
- **Purpose:** Indicates if this is a lease procurement
- **Visibility:** Always visible
- **Triggers:** Shows/hides SRM Compliance Document field

---

### **21. SRM Compliance Document**
- **Field Type:** File Upload
- **Required:** No
- **Accepted Formats:** .pdf, .doc, .docx, .xls, .xlsx
- **Purpose:** Upload SRM compliance document for lease
- **Visibility:** ✨ **Visible only if Is this a Lease = Yes**

---

## 🔧 Technical Implementation

### **Global State Management**

```javascript
let generalInfoValues = {
  sourcingOrSolicitation: '',
  modalityOfBidReceipt: '',
  epp: '',
  medicineOrMedical: '',
  processType: [],              // Array for multiple selections
  directContracting: false,
  unCollaboration: '',
  isLease: ''
};
```

### **Update Functions**

#### **1. updateSourcingOrSolicitation(value)**
- Updates `sourcingOrSolicitation` value
- Re-renders block to show/hide dependent fields
- **Affected Fields:** Sourcing Method, Process Type, Solicitation Method

#### **2. updateModalityOfBidReceipt(value)**
- Updates `modalityOfBidReceipt` value
- Re-renders block
- **Affected Fields:** Waiver for Bid Receipt Modality

#### **3. updateEPP(value)**
- Updates `epp` value
- Re-renders block
- **Affected Fields:** EPP Authorization (Jira Link), EPP Authorization End Date, Scope of EPP Approval

#### **4. updateMedicineOrMedical(value)**
- Updates `medicineOrMedical` value
- Re-renders block
- **Affected Fields:** Policy Exception Link

#### **5. updateProcessType(checkbox)**
- Handles checkbox changes for Process Type
- Updates `processType` array
- Updates `directContracting` flag
- Re-renders block
- **Affected Fields:** Contract Amendment, Linked Process

#### **6. updateUNCollaboration(value)**
- Updates `unCollaboration` value
- Re-renders block
- **Affected Fields:** UN Collaboration Type, UN Agencies

#### **7. updateIsLease(value)**
- Updates `isLease` value
- Re-renders block
- **Affected Fields:** SRM Compliance Document

### **Conditional Visibility Function**

```javascript
window.shouldShowField = function(field) {
  if (!field.conditionalVisibility) return true;
  
  const condition = field.conditionalVisibility;
  
  // Complex condition for Linked Process
  if (condition.complex && field.id === 'linkedProcess') {
    const hasDirectContracting = generalInfoValues.processType.includes('Direct contracting / Sole sourcing');
    const hasSolicitation = generalInfoValues.sourcingOrSolicitation !== '';
    const hasLTABPA = generalInfoValues.processType.includes('Solicit offers against LTA/BPA');
    const hasContractAmendment = generalInfoValues.processType.includes('Contract Amendment');
    return hasDirectContracting || hasSolicitation || hasLTABPA || hasContractAmendment;
  }
  
  // Single value condition
  if (condition.field && condition.value) {
    return generalInfoValues[condition.field] === condition.value;
  }
  
  // Multiple values condition
  if (condition.field && condition.values) {
    return condition.values.includes(generalInfoValues[condition.field]);
  }
  
  // Contains condition (for checkboxes)
  if (condition.field && condition.contains) {
    return generalInfoValues.processType.includes(condition.contains);
  }
  
  return true;
}
```

---

## 🎨 New Field Types

### **1. Checkbox Group**
**Usage:** Process Type field

**Rendering:**
```html
<div class="form-group full-width">
  <label class="form-label">Process Type</label>
  <div style="display: flex; flex-direction: column; gap: var(--spacing-sm);">
    <label style="display: flex; align-items: center; gap: 8px; ...">
      <input type="checkbox" value="Standard solicitation" onchange="updateProcessType(this)" />
      <span>Standard solicitation</span>
    </label>
    <!-- More checkboxes -->
  </div>
</div>
```

### **2. Search Field**
**Usage:** Linked Process, Organizational Unit

**Rendering:**
```html
<div class="form-group full-width">
  <label class="form-label">Linked Process</label>
  <div style="position: relative;">
    <input type="text" class="form-input" placeholder="Search by Event ID" style="padding-left: 40px;" />
    <span style="position: absolute; left: 12px; top: 50%; ...">🔍</span>
  </div>
</div>
```

### **3. File Upload**
**Usage:** SRM Compliance Document

**Rendering:**
```html
<div class="form-group full-width">
  <label class="form-label">SRM Compliance Document</label>
  <div style="display: flex; gap: var(--spacing-md); align-items: center;">
    <input type="file" class="form-input" accept=".pdf,.doc,.docx,.xls,.xlsx" style="flex: 1;" />
    <button class="btn btn-secondary btn-sm" type="button">📎 Choose File</button>
  </div>
</div>
```

---

## 🧪 Testing Guide

### **Test 1: Sourcing vs Solicitation**
1. Open `tender-create.html`
2. Navigate to "General Information" block
3. Select "Sourcing" from "Sourcing or Solicitation" dropdown
4. **Expected:** "Sourcing Method" field appears
5. **Expected:** "Process Type" and "Solicitation Method" fields are hidden
6. Select "Solicitation"
7. **Expected:** "Sourcing Method" field disappears
8. **Expected:** "Process Type" and "Solicitation Method" fields appear

---

### **Test 2: Modality of Bid Receipt**
1. Select "Online Only" from "Modality of Bid Receipt"
2. **Expected:** "Waiver for Bid Receipt Modality" field is hidden
3. Select "Email Submission"
4. **Expected:** "Waiver for Bid Receipt Modality" field appears
5. Select "Physical Submission"
6. **Expected:** "Waiver for Bid Receipt Modality" field remains visible

---

### **Test 3: EPP Fields**
1. Select "No" from "EPP" dropdown
2. **Expected:** All EPP-related fields hidden:
   - EPP Authorization (Jira Link)
   - EPP Authorization End Date
   - Scope of EPP Approval
3. Select "Yes" from "EPP" dropdown
4. **Expected:** All 3 EPP-related fields appear

---

### **Test 4: Medicine/Medical Equipment**
1. Select "No" from "Medicine / Medical Equipment"
2. **Expected:** "Policy Exception" field hidden
3. Select "Yes"
4. **Expected:** "Policy Exception for Medicine/Medical Equipment Procurement (Annex 2 Jira Link)" field appears

---

### **Test 5: Process Type (Checkbox Group)**
1. Select "Solicitation" from "Sourcing or Solicitation"
2. **Expected:** "Process Type" checkbox group appears
3. Check "Standard solicitation"
4. **Expected:** Field value updated, no other fields affected
5. Check "Direct contracting / Sole sourcing"
6. **Expected:** "Contract Amendment" dropdown appears
7. Uncheck "Direct contracting / Sole sourcing"
8. **Expected:** "Contract Amendment" dropdown disappears

---

### **Test 6: Linked Process (Complex Visibility)**
1. Initial state: **Expected:** "Linked Process" hidden
2. Select "Solicitation" from "Sourcing or Solicitation"
3. **Expected:** "Linked Process" appears (because hasSolicitation = true)
4. Check "Direct contracting / Sole sourcing" in Process Type
5. **Expected:** "Linked Process" remains visible
6. Check "Solicit offers against LTA/BPA"
7. **Expected:** "Linked Process" remains visible

---

### **Test 7: UN Collaboration**
1. Select "No" from "UN Collaboration"
2. **Expected:** "UN Collaboration Type" and "UN Agencies" fields hidden
3. Select "Yes"
4. **Expected:** Both "UN Collaboration Type" and "UN Agencies" fields appear
5. Select multiple agencies from "UN Agencies"
6. **Expected:** Multiple selections work (Hold Ctrl/Cmd)

---

### **Test 8: Lease and SRM Document**
1. Select "No" from "Is this a Lease"
2. **Expected:** "SRM Compliance Document" file upload hidden
3. Select "Yes"
4. **Expected:** "SRM Compliance Document" file upload appears
5. Click "📎 Choose File" button
6. **Expected:** File picker opens

---

### **Test 9: Search Fields**
1. Check "Linked Process" field (when visible)
2. **Expected:** Search icon (🔍) visible on left
3. Type in search field
4. **Expected:** Input works normally with icon present
5. Check "Organizational Unit" field
6. **Expected:** Same search field behavior

---

### **Test 10: File Upload**
1. Make "Is this a Lease" = Yes
2. **Expected:** File upload field appears
3. Click file input or "Choose File" button
4. **Expected:** File picker opens
5. **Expected:** Only accepts .pdf, .doc, .docx, .xls, .xlsx files

---

## 📊 Conditional Visibility Summary

### **Fields Always Visible:**
1. Title
2. Reference
3. Sourcing or Solicitation
4. Type of Requirement
5. Description
6. Beneficiary country(ies)
7. Modality of Bid Receipt
8. EPP
9. Medicine / Medical Equipment
10. UN Collaboration
11. Organizational Unit
12. Is this a Lease

### **Conditionally Visible Fields:**

| Field | Visible When | Parent Field |
|-------|-------------|--------------|
| Sourcing Method | Sourcing selected | Sourcing or Solicitation |
| Waiver for Bid Receipt | Email/Physical selected | Modality of Bid Receipt |
| EPP Authorization (Jira Link) | EPP = Yes | EPP |
| EPP Authorization End Date | EPP = Yes | EPP |
| Scope of EPP Approval | EPP = Yes | EPP |
| Policy Exception Link | Medicine = Yes | Medicine / Medical Equipment |
| Process Type | Solicitation selected | Sourcing or Solicitation |
| Contract Amendment | Direct contracting checked | Process Type |
| Solicitation Method | Solicitation selected | Sourcing or Solicitation |
| Linked Process | Complex condition met | Multiple fields |
| UN Collaboration Type | UN Collaboration = Yes | UN Collaboration |
| UN Agencies | UN Collaboration = Yes | UN Collaboration |
| SRM Compliance Document | Is Lease = Yes | Is this a Lease |

---

## ⚠️ No Breaking Changes

All existing features work exactly as before:
- ✅ All other blocks and sections
- ✅ Navigation and data persistence
- ✅ Form validation
- ✅ Wizard data pre-population for existing fields

---

## 📁 Files Modified

- ✅ `tender-create.html` - All changes implemented
- ✅ `GENERAL_INFO_FIELDS_SUMMARY.md` - Comprehensive documentation

---

## ✨ Summary

**All requested fields successfully added:**

✅ 21 new/updated fields in General Information block  
✅ Complex conditional visibility logic implemented  
✅ 3 new field types (checkbox-group, search, file)  
✅ 7 update functions for dynamic field visibility  
✅ Comprehensive state management  
✅ No breaking changes - all existing features work  
✅ Clean, modern UI with UNOPS design system  

The General Information block now provides comprehensive tender/sourcing configuration with intelligent conditional fields! 🎉

