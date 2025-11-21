# Evaluation Criteria - Feature Summary

## Overview
This document summarizes the improvements made to the Evaluation block in `tender-create.html`, allowing tender authors to define comprehensive evaluation criteria for assessing supplier submissions.

---

## ✅ Changes Implemented

### **1. Block Renamed** ✏️

**Change:** "Evaluation Criteria" → **"Evaluation"**

**Files Modified:**
- Line 2312: Block name updated
- Line 2161: Form library label updated

**Before:**
```javascript
{
  id: 'evaluation',
  icon: '📊',
  name: 'Evaluation Criteria',
  // ...
}
```

**After:**
```javascript
{
  id: 'evaluation',
  icon: '📊',
  name: 'Evaluation',
  // ...
}
```

---

### **2. New Section Added** ✨

**Feature:** "Evaluation Criteria" section within the Evaluation block

**Implementation:**
- Added new section with special flag `isEvaluationCriteria: true`
- Custom rendering logic to display dynamic criteria management interface
- Section includes:
  - "+ Add Evaluation Criterion" button
  - Empty state when no criteria defined
  - List of all defined criteria with full management capabilities

**Code:**
```javascript
{
  title: 'Evaluation Criteria',
  isEvaluationCriteria: true, // Special flag for custom rendering
}
```

---

### **3. Evaluation Criterion Data Structure** 📊

**Structure:** Each evaluation criterion contains:

```javascript
{
  id: 'ec_1',                              // Unique identifier
  type: 'Technical criteria',              // Criterion type
  description: 'Technical specifications', // Detailed description
  evaluationMethod: 'Scoring (0-100)',    // How it's evaluated
  appliesTo: 'specific',                   // 'entire' or 'specific'
  unspscCodes: ['43211503', '43000000']   // Selected UNSPSC codes (if specific)
}
```

**Global State:**
```javascript
let evaluationCriteria = [];              // Array of all criteria
let evaluationCriteriaIdCounter = 1;      // ID generator
```

---

### **4. Criterion Fields** 📝

#### **a) Type** (Required)
**Field Type:** Dropdown  
**Options:**
- Eligibility and formal criteria
- Qualification criteria
- Technical criteria
- Shortlisting criteria

**Purpose:** Categorizes the criterion for organizational purposes

---

#### **b) Description** (Required)
**Field Type:** Multi-line text area  
**Purpose:** Detailed explanation of what will be evaluated and how  
**Example:** "Supplier must demonstrate at least 5 years of experience in providing similar products or services"

---

#### **c) Evaluation Method** (Required)
**Field Type:** Dropdown  
**Options:**
- Pass/Fail
- Scoring (0-100)
- Weighted Scoring
- Ranking

**Purpose:** Defines how the criterion will be assessed

---

#### **d) Applies To** (Required)
**Field Type:** Radio buttons + Multi-select dropdown

**Option 1: Entire Tender**
- Criterion applies to the entire tender submission
- No additional configuration needed

**Option 2: Select specific UNSPSC codes**
- Criterion applies only to selected UNSPSC codes
- Multi-select dropdown appears with available UNSPSC codes
- UNSPSC codes sourced from:
  - Requisition lines (from "Requisition & Lots" block)
  - Lots with products (from "Requisition & Lots" block)
- Hold Ctrl/Cmd to select multiple codes

**Dynamic Behavior:**
- If "Entire Tender" selected → Multi-select dropdown hidden, `unspscCodes` array cleared
- If "Specific UNSPSC codes" selected → Multi-select dropdown appears
- If no UNSPSC codes available → Warning message displayed

---

### **5. Management Functions** ⚙️

#### **Add Evaluation Criterion**
```javascript
window.addEvaluationCriterion = function()
```
- Creates new criterion with default values
- Adds to `evaluationCriteria` array
- Re-renders current block

**Default Values:**
- Type: "Eligibility and formal criteria"
- Description: "" (empty)
- Evaluation Method: "Pass/Fail"
- Applies To: "entire"
- UNSPSC Codes: [] (empty array)

---

#### **Remove Evaluation Criterion**
```javascript
window.removeEvaluationCriterion = function(criterionId)
```
- Shows confirmation dialog
- Removes criterion from array
- Re-renders current block

---

#### **Update Criterion Type**
```javascript
window.updateEvaluationCriterionType = function(criterionId, value)
```
- Updates the criterion type dropdown value

---

#### **Update Criterion Description**
```javascript
window.updateEvaluationCriterionDescription = function(criterionId, value)
```
- Updates the free-text description

---

#### **Update Criterion Evaluation Method**
```javascript
window.updateEvaluationCriterionMethod = function(criterionId, value)
```
- Updates the evaluation method dropdown value

---

#### **Update Criterion Applies To**
```javascript
window.updateEvaluationCriterionAppliesTo = function(criterionId, value)
```
- Updates the "Applies To" selection (entire/specific)
- If switched to "entire", clears `unspscCodes` array
- Re-renders to show/hide UNSPSC multi-select

---

#### **Update Criterion UNSPSC Codes**
```javascript
window.updateEvaluationCriterionUnspscCodes = function(criterionId, selectElement)
```
- Updates the selected UNSPSC codes from multi-select dropdown
- Stores selected codes in criterion's `unspscCodes` array

---

#### **Get Available UNSPSC Codes**
```javascript
window.getAvailableUnspscCodes = function()
```
- Retrieves all unique UNSPSC codes from:
  - `requisitionLines` array (from ERP integration)
  - `lots` array (user-created lots with products)
- Returns array of objects: `{ code, description }`
- Eliminates duplicates

---

### **6. Rendering Function** 🎨

#### **renderEvaluationCriteriaSection(block, section, sectionIndex, isFirst)**

**Purpose:** Custom rendering for the Evaluation Criteria section

**Features:**
- Section header with icon and title
- Info alert explaining the section's purpose
- "+ Add Evaluation Criterion" button (top right)
- Empty state when no criteria defined
- List of criteria cards with all fields and delete button
- Dynamic UNSPSC multi-select based on "Applies To" selection
- Warning message if no UNSPSC codes available

**Layout:** Card-based layout with form grid for fields

---

## 🎨 UI Design

### **Section Header**
```
📊 Evaluation Criteria
ℹ️ Define evaluation criteria that will be used to assess supplier submissions

                                              [+ Add Evaluation Criterion]
```

---

### **Empty State**
```
┌─────────────────────────────────────────┐
│              📊                          │
│   No evaluation criteria defined         │
│   Click "+ Add Evaluation Criterion" to  │
│   define how submissions will be         │
│   evaluated                              │
└─────────────────────────────────────────┘
```

---

### **Criterion Card**
```
┌─────────────────────────────────────────────────────┐
│ Criterion 1                                    [✖]  │
├─────────────────────────────────────────────────────┤
│ Type *                    Evaluation Method *       │
│ [Technical criteria▼]     [Scoring (0-100)▼]       │
│                                                     │
│ Description *                                       │
│ [_____________________________________________]     │
│ [_____________________________________________]     │
│ [_____________________________________________]     │
│ Provide a clear description of what will be        │
│ evaluated and how                                  │
│                                                     │
│ Applies To *                                        │
│ ○ Entire Tender                                    │
│ ● Specific UNSPSC Codes                            │
│                                                     │
│ Select UNSPSC Codes:                               │
│ ┌─────────────────────────────────────────┐       │
│ │ 43211503 - Laptop computers          ☑  │       │
│ │ 43000000 - Computer equipment        ☑  │       │
│ │ 44000000 - Office supplies           ☐  │       │
│ │ 45000000 - Furniture                 ☐  │       │
│ └─────────────────────────────────────────┘       │
│ Hold Ctrl/Cmd to select multiple UNSPSC codes     │
└─────────────────────────────────────────────────────┘
```

---

## 🧪 Testing Guide

### **Test 1: Navigate to Evaluation Block**
1. Open `tender-create.html`
2. Navigate through blocks until you reach "Evaluation" (📊 icon)
3. **Expected:** Block name is "Evaluation" (not "Evaluation Criteria")
4. **Expected:** Two sections visible:
   - "Evaluation Method" (existing)
   - "Evaluation Criteria" (new)

---

### **Test 2: Add Evaluation Criterion**
1. In "Evaluation Criteria" section, click "+ Add Evaluation Criterion"
2. **Expected:** New criterion card appears with:
   - Header "Criterion 1" with delete button
   - Type dropdown (default: "Eligibility and formal criteria")
   - Evaluation Method dropdown (default: "Pass/Fail")
   - Description text area (empty)
   - Applies To radio buttons (default: "Entire Tender" selected)
3. Fill in all fields:
   - Type: "Technical criteria"
   - Evaluation Method: "Scoring (0-100)"
   - Description: "Supplier must demonstrate technical capability"
   - Keep "Entire Tender" selected
4. **Expected:** All values saved

---

### **Test 3: Applies To - Entire Tender**
1. Add a criterion
2. Select "Entire Tender" in "Applies To"
3. **Expected:** No UNSPSC multi-select dropdown visible
4. **Expected:** Criterion applies to entire tender submission

---

### **Test 4: Applies To - Specific UNSPSC (No Codes Available)**
1. Add a criterion
2. Select "Specific UNSPSC Codes" in "Applies To"
3. **Expected (if no requisition lines or lots):** Warning message:
   ```
   ⚠️ No UNSPSC codes available. Please add requisition lines or 
   lots with products first.
   ```

---

### **Test 5: Applies To - Specific UNSPSC (With Codes)**
1. First, go to "Requisition & Lots" block
2. Add requisition lines or create lots with products
3. Return to "Evaluation" block → "Evaluation Criteria"
4. Add a criterion
5. Select "Specific UNSPSC Codes" in "Applies To"
6. **Expected:** Multi-select dropdown appears with all available UNSPSC codes
7. Select multiple codes (hold Ctrl/Cmd)
8. **Expected:** Selected codes are highlighted
9. **Expected:** Criterion applies only to selected UNSPSC codes

---

### **Test 6: Remove Evaluation Criterion**
1. Add 2-3 evaluation criteria
2. Click ✖ button on the second criterion
3. **Expected:** Confirmation dialog appears
4. Click "OK"
5. **Expected:** Criterion is removed from list
6. **Expected:** Remaining criteria stay intact
7. **Expected:** Criterion numbers update (Criterion 1, Criterion 2, etc.)

---

### **Test 7: Multiple Criteria**
1. Add multiple evaluation criteria with different configurations:
   - Criterion 1: Eligibility, Pass/Fail, Entire Tender
   - Criterion 2: Technical, Scoring (0-100), Specific UNSPSC
   - Criterion 3: Qualification, Weighted Scoring, Specific UNSPSC
2. **Expected:** All criteria display correctly with their configurations
3. Navigate to another block and back
4. **Expected:** All criteria remain as configured (data persistence)

---

### **Test 8: Dropdown Options**
1. Add a criterion
2. Check Type dropdown options:
   - ✓ Eligibility and formal criteria
   - ✓ Qualification criteria
   - ✓ Technical criteria
   - ✓ Shortlisting criteria
3. Check Evaluation Method dropdown options:
   - ✓ Pass/Fail
   - ✓ Scoring (0-100)
   - ✓ Weighted Scoring
   - ✓ Ranking

---

### **Test 9: Form Library Update**
1. Navigate to "Submission Requirements" or any block with form library
2. Open "Form Library" panel
3. **Expected:** Form shows as "Evaluation Form" (not "Evaluation Criteria Form")

---

### **Test 10: UNSPSC Integration**
1. Go to "Requisition & Lots" block
2. Add requisition lines with UNSPSC: 43211503, 43000000
3. Create a lot "Electronics" with UNSPSC: 44000000
4. Go to "Evaluation" block → "Evaluation Criteria"
5. Add criterion with "Specific UNSPSC Codes"
6. **Expected:** Multi-select shows all 3 UNSPSC codes:
   - 43211503 - Laptop computers
   - 43000000 - Computer equipment
   - 44000000 - (Description from lot)
7. Select code 43211503
8. **Expected:** Only this code is stored in criterion

---

## ⚠️ No Breaking Changes

### **✅ Still Working:**
- All existing "Evaluation Method" section functionality
- All other blocks and sections
- Requisition & Lots management
- Product Requirements
- Bid Response Requirements
- All navigation and data persistence

### **✨ Enhanced:**
- Evaluation block now has comprehensive criteria management
- Tender authors can define multiple evaluation criteria
- Criteria can apply to entire tender or specific UNSPSC codes
- Dynamic integration with requisition lines and lots

---

## 📁 Code Structure

### **Global State:**
```javascript
let evaluationCriteria = [];              // Array of criterion objects
let evaluationCriteriaIdCounter = 1;      // ID generator
```

### **Criterion Object:**
```javascript
{
  id: 'ec_1',
  type: 'Technical criteria',
  description: 'Detailed criterion description',
  evaluationMethod: 'Scoring (0-100)',
  appliesTo: 'specific',                  // 'entire' or 'specific'
  unspscCodes: ['43211503', '43000000']
}
```

### **Key Functions:**
```javascript
addEvaluationCriterion()
removeEvaluationCriterion(criterionId)
updateEvaluationCriterionType(criterionId, value)
updateEvaluationCriterionDescription(criterionId, value)
updateEvaluationCriterionMethod(criterionId, value)
updateEvaluationCriterionAppliesTo(criterionId, value)
updateEvaluationCriterionUnspscCodes(criterionId, selectElement)
getAvailableUnspscCodes()
renderEvaluationCriteriaSection(block, section, sectionIndex, isFirst)
```

---

## 💡 Implementation Details

### **1. UNSPSC Code Retrieval**
```javascript
window.getAvailableUnspscCodes = function() {
  let unspscCodes = [];
  
  // From requisition lines
  requisitionLines.forEach(line => {
    if (!unspscCodes.some(u => u.code === line.unspscCode)) {
      unspscCodes.push({
        code: line.unspscCode,
        description: line.unspscDescription
      });
    }
  });
  
  // From lots
  lots.forEach(lot => {
    lot.unspscCodes.forEach(unspsc => {
      if (!unspscCodes.some(u => u.code === unspsc.code)) {
        unspscCodes.push({
          code: unspsc.code,
          description: unspsc.description || 'No description'
        });
      }
    });
  });
  
  return unspscCodes;
}
```

### **2. Dynamic Applies To Rendering**
```javascript
${criterion.appliesTo === 'specific' ? `
  <div style="margin-top: var(--spacing-md);">
    ${availableUnspscCodes.length === 0 ? `
      <div>⚠️ No UNSPSC codes available...</div>
    ` : `
      <select multiple ...>
        ${availableUnspscCodes.map(unspsc => `
          <option 
            value="${unspsc.code}" 
            ${criterion.unspscCodes.includes(unspsc.code) ? 'selected' : ''}
          >
            ${unspsc.code} - ${unspsc.description}
          </option>
        `).join('')}
      </select>
    `}
  </div>
` : ''}
```

### **3. Special Section Rendering**
```javascript
// In renderCurrentBlock() function
if (section.isEvaluationCriteria) {
  html += renderEvaluationCriteriaSection(block, section, sectionIndex, isFirst);
  return;
}
```

---

## 📊 Use Cases

### **Use Case 1: Basic Eligibility Check**
**Scenario:** Tender requires all suppliers to be registered entities

**Configuration:**
- Type: "Eligibility and formal criteria"
- Description: "Supplier must be a legally registered entity with valid business registration"
- Evaluation Method: "Pass/Fail"
- Applies To: "Entire Tender"

---

### **Use Case 2: Product-Specific Technical Requirements**
**Scenario:** Laptops must meet specific technical specifications

**Configuration:**
- Type: "Technical criteria"
- Description: "Laptops must have minimum 16GB RAM, 512GB SSD, Intel i7 processor"
- Evaluation Method: "Scoring (0-100)"
- Applies To: "Specific UNSPSC Codes" → Select "43211503 - Laptop computers"

---

### **Use Case 3: Multiple Product Categories with Different Criteria**
**Scenario:** Different evaluation for laptops vs. office furniture

**Configuration:**
- **Criterion 1:**
  - Type: "Technical criteria"
  - Description: "Laptops: Technical specifications compliance"
  - Evaluation Method: "Scoring (0-100)"
  - Applies To: "43211503 - Laptop computers"

- **Criterion 2:**
  - Type: "Technical criteria"
  - Description: "Furniture: Ergonomic design and sustainability certification"
  - Evaluation Method: "Weighted Scoring"
  - Applies To: "45000000 - Furniture"

---

## ✨ Summary

**All requested improvements successfully implemented:**

✅ Block renamed from "Evaluation Criteria" to "Evaluation"  
✅ New "Evaluation Criteria" section added  
✅ Dynamic criterion management (add/remove)  
✅ Four required fields per criterion:
   - Type (dropdown with 4 options)
   - Description (free text area)
   - Evaluation Method (dropdown with 4 options)
   - Applies To (radio + multi-select for UNSPSC)  
✅ Integration with Requisition & Lots (UNSPSC codes)  
✅ Empty state when no codes available  
✅ No breaking changes - all existing features work  
✅ Clean, modern UI with UNOPS design system  

The Evaluation block now provides comprehensive criteria management for assessing supplier submissions! 🎉

