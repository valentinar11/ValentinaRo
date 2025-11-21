# Template Builder Workspace - Comprehensive Enhancement Plan

## Executive Summary

This document outlines the comprehensive plan to enhance `template-builder-workspace.html` to include all fields, configurations, and logic from `tender-create.html`, adapted for an admin template configuration environment.

---

## Scope of Enhancement

### **From Tender Create (what we're replicating):**

#### **1. General Information Block (25 fields)**
- Title, Reference, Sourcing/Solicitation (with conditional fields)
- Modality of Bid Receipt (with waiver)
- EPP fields (4 conditional fields)
- Medicine/Medical Equipment (with policy exception)
- Process Type (checkbox group with Contract Amendment)
- Solicitation Method, Linked Process
- UN Collaboration (with type and agencies)
- Organizational Unit, Lease fields

#### **2. Project & Tender Details Block (67 fields)**
- Project Information (4 search fields from oneUNOPS)
- **Particulars Section (63 fields)**:
  - Basic Particulars (8 fields)
  - Pre-Bid Meetings (conditional, 0-6 fields)
  - Site Visit (conditional, 3 fields)
  - Exclusivity (conditional, 1 field)
  - Commercial Conditions (5 fields)
  - Alternative Offers (conditional, 1 field)
  - Contracting Details (4 fields)
  - Payment Terms & Advance Payments (conditional, up to 11 fields)
  - Additional Payment Requirements (3 fields)
  - Damages & Securities (conditional, up to 11 fields)

#### **3. Requisition & Lots Block**
- Requisition Lines management
- Lots creation and management
- UNSPSC code assignment with quantities
- Dynamic lot structure

#### **4. Scope & Requirements Block**
- Product Requirements & Specifications per UNSPSC
  - Attributes mode vs Description mode (mutually exclusive)
  - Rich text editor for descriptions
  - Structured attribute name-value pairs
  - Export/Upload spreadsheet functionality
- Tender Author Configuration
  - Service Descriptions
  - Delivery Requirements (configurable fields)
  - Specific Requirements

#### **5. Submission Requirements Block**
- Bid Response Requirements (5 sections A-E)
  - Each section with add/delete field capability
  - Per Bid, Per UNSPSC, Per Attribute, Per Service Line, Per Delivery
- Supplier Form Preview (toggleable)

#### **6. Evaluation Block**
- Evaluation Method (5 fields with conditional visibility)
- Evaluation Criteria (dynamic, with UNSPSC targeting)
  - Type, Description, Evaluation Method, Applies To

#### **7. Team Block (7 fields)**
- All search fields from oneUNOPS RDAT
- Multiple role assignments

---

## Conversion Pattern: Tender Author → Admin Configuration

### **Core Principle:**

Every field a tender author fills in `tender-create.html` becomes a **configurable template field** in `template-builder-workspace.html`.

### **Admin Configuration Properties for Each Field:**

For EVERY field, the admin can configure:

1. **Field Label** - Text input for custom label
2. **Field Type** - Dropdown (text, textarea, dropdown, checkbox, date, etc.)
3. **Required** - Checkbox (Is this field required?)
4. **Default Value** - Input/dropdown for pre-filled value
5. **Help Text** - Textarea for helper text
6. **Placeholder** - Text for placeholder text
7. **Conditional Visibility** - Rules builder
8. **Allowed Values** - For dropdowns/multi-selects
9. **Validation Rules** - Min/max, regex, etc.

### **Example Conversion:**

#### **Tender Create Field (Author View):**
```html
<div class="form-group">
  <label class="form-label">Title <span class="required">*</span></label>
  <input type="text" class="form-input" required />
</div>
```

#### **Template Builder Field (Admin Configuration):**
```html
<div class="template-field-config">
  <div class="config-header">
    <h4>Title Field Configuration</h4>
    <button class="btn-icon-sm" onclick="removeField('title')">✖</button>
  </div>
  
  <div class="config-body">
    <div class="form-group">
      <label>Field Label</label>
      <input type="text" value="Title" onchange="updateFieldLabel('title', this.value)" />
    </div>
    
    <div class="form-group">
      <label>Field Type</label>
      <select onchange="updateFieldType('title', this.value)">
        <option value="text" selected>Text</option>
        <option value="textarea">Textarea</option>
        <option value="dropdown">Dropdown</option>
      </select>
    </div>
    
    <div class="form-group">
      <label class="checkbox-label">
        <input type="checkbox" checked onchange="updateFieldRequired('title', this.checked)" />
        Required Field
      </label>
    </div>
    
    <div class="form-group">
      <label>Default Value</label>
      <input type="text" placeholder="Leave empty for no default" onchange="updateFieldDefault('title', this.value)" />
    </div>
    
    <div class="form-group">
      <label>Help Text</label>
      <textarea placeholder="Provide guidance for tender authors..." onchange="updateFieldHelp('title', this.value)"></textarea>
    </div>
    
    <div class="form-group">
      <label>Validation Rules</label>
      <div class="validation-rules">
        <input type="number" placeholder="Min length" onchange="updateFieldValidation('title', 'minLength', this.value)" />
        <input type="number" placeholder="Max length" onchange="updateFieldValidation('title', 'maxLength', this.value)" />
      </div>
    </div>
  </div>
</div>
```

---

## Implementation Approach

### **Phase 1: Data Structure Enhancement**

#### **1.1 Template Configuration Schema**

```javascript
let templateConfig = {
  templateId: 'template-001',
  templateName: 'Standard RFQ Template',
  blocks: [
    {
      blockId: 'general-info',
      blockName: 'General Information',
      blockIcon: '📋',
      enabled: true,
      sections: [
        {
          sectionId: 'basic-info',
          sectionName: 'Basic Information',
          enabled: true,
          fields: [
            {
              fieldId: 'title',
              fieldLabel: 'Title',
              fieldType: 'text',
              required: true,
              enabled: true,
              defaultValue: '',
              placeholder: 'Enter tender title',
              helpText: 'Provide a clear, descriptive title',
              validation: {
                minLength: 10,
                maxLength: 200,
                pattern: null
              },
              conditionalVisibility: null,
              position: 1
            },
            {
              fieldId: 'sourcingOrSolicitation',
              fieldLabel: 'Sourcing or Solicitation',
              fieldType: 'dropdown',
              required: true,
              enabled: true,
              defaultValue: '',
              options: ['Sourcing', 'Solicitation'],
              helpText: 'Select the type of procurement process',
              conditionalVisibility: null,
              triggersConditionalFields: ['sourcingMethod', 'processType', 'solicitationMethod'],
              position: 2
            },
            {
              fieldId: 'sourcingMethod',
              fieldLabel: 'Sourcing Method',
              fieldType: 'dropdown',
              required: false,
              enabled: true,
              defaultValue: '',
              options: ['RFI', 'EOI', 'PQ', 'Ad-hoc shortlist'],
              helpText: 'Select the specific sourcing method',
              conditionalVisibility: {
                field: 'sourcingOrSolicitation',
                operator: 'equals',
                value: 'Sourcing'
              },
              position: 3
            }
            // ... all other fields
          ]
        }
      ]
    }
    // ... all other blocks
  ],
  conditionalRules: [
    {
      ruleId: 'rule-001',
      ruleName: 'Show Sourcing Method when Sourcing selected',
      condition: {
        field: 'sourcingOrSolicitation',
        operator: 'equals',
        value: 'Sourcing'
      },
      actions: [
        { type: 'show', targetFields: ['sourcingMethod'] },
        { type: 'hide', targetFields: ['processType', 'solicitationMethod'] }
      ]
    }
    // ... all other rules
  ],
  validationRules: [
    {
      ruleId: 'val-001',
      field: 'title',
      validationType: 'required',
      errorMessage: 'Title is required'
    }
    // ... all validation rules
  ]
};
```

#### **1.2 Field Configuration Components**

Create reusable configuration components for different field types:

```javascript
const fieldConfigComponents = {
  text: {
    configFields: ['label', 'required', 'defaultValue', 'placeholder', 'helpText', 'minLength', 'maxLength', 'pattern'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  },
  textarea: {
    configFields: ['label', 'required', 'defaultValue', 'placeholder', 'helpText', 'minLength', 'maxLength', 'rows'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  },
  dropdown: {
    configFields: ['label', 'required', 'defaultValue', 'options', 'allowMultiple', 'helpText'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  },
  checkbox: {
    configFields: ['label', 'required', 'defaultValue', 'helpText'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  },
  checkboxGroup: {
    configFields: ['label', 'required', 'options', 'defaultValues', 'helpText'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  },
  date: {
    configFields: ['label', 'required', 'defaultValue', 'minDate', 'maxDate', 'helpText'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  },
  search: {
    configFields: ['label', 'required', 'dataSource', 'placeholder', 'helpText', 'allowMultiple'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  },
  file: {
    configFields: ['label', 'required', 'acceptedTypes', 'maxSize', 'helpText'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  },
  richText: {
    configFields: ['label', 'required', 'defaultValue', 'helpText', 'enabledTools'],
    render: (fieldId, fieldConfig) => { /* ... */ }
  }
};
```

---

### **Phase 2: UI Components**

#### **2.1 Field Configuration Panel**

Create a reusable panel for configuring any field:

```javascript
function renderFieldConfigPanel(blockId, sectionId, fieldId, fieldConfig) {
  return `
    <div class="field-config-panel" data-field-id="${fieldId}">
      <div class="config-panel-header">
        <div class="config-panel-title">
          <span class="field-type-icon">${getFieldTypeIcon(fieldConfig.fieldType)}</span>
          <h4>${fieldConfig.fieldLabel || 'Untitled Field'}</h4>
        </div>
        <div class="config-panel-actions">
          <button class="btn-icon" onclick="duplicateField('${blockId}', '${sectionId}', '${fieldId}')" title="Duplicate">📋</button>
          <button class="btn-icon" onclick="moveFieldUp('${blockId}', '${sectionId}', '${fieldId}')" title="Move Up">↑</button>
          <button class="btn-icon" onclick="moveFieldDown('${blockId}', '${sectionId}', '${fieldId}')" title="Move Down">↓</button>
          <button class="btn-icon btn-danger" onclick="deleteField('${blockId}', '${sectionId}', '${fieldId}')" title="Delete">🗑️</button>
        </div>
      </div>
      
      <div class="config-panel-body">
        <!-- Basic Configuration -->
        <div class="config-section">
          <h5 class="config-section-title">Basic Configuration</h5>
          
          <div class="form-group">
            <label class="form-label">Field Label</label>
            <input type="text" class="form-input" value="${fieldConfig.fieldLabel || ''}" 
                   onchange="updateFieldConfig('${blockId}', '${sectionId}', '${fieldId}', 'fieldLabel', this.value)" />
          </div>
          
          <div class="form-group">
            <label class="form-label">Field Type</label>
            <select class="form-select" onchange="updateFieldConfig('${blockId}', '${sectionId}', '${fieldId}', 'fieldType', this.value)">
              ${Object.keys(fieldConfigComponents).map(type => 
                `<option value="${type}" ${fieldConfig.fieldType === type ? 'selected' : ''}>${type}</option>`
              ).join('')}
            </select>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" ${fieldConfig.required ? 'checked' : ''} 
                     onchange="updateFieldConfig('${blockId}', '${sectionId}', '${fieldId}', 'required', this.checked)" />
              Required Field
            </label>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" ${fieldConfig.enabled ? 'checked' : ''} 
                     onchange="updateFieldConfig('${blockId}', '${sectionId}', '${fieldId}', 'enabled', this.checked)" />
              Field Enabled
            </label>
          </div>
        </div>
        
        <!-- Field-Specific Configuration -->
        <div class="config-section">
          <h5 class="config-section-title">Field-Specific Settings</h5>
          ${renderFieldSpecificConfig(fieldConfig)}
        </div>
        
        <!-- Conditional Visibility -->
        <div class="config-section">
          <h5 class="config-section-title">Conditional Visibility</h5>
          <button class="btn btn-secondary btn-sm" onclick="openConditionalRulesBuilder('${blockId}', '${sectionId}', '${fieldId}')">
            ${fieldConfig.conditionalVisibility ? '✏️ Edit Rules' : '➕ Add Rules'}
          </button>
          ${fieldConfig.conditionalVisibility ? renderConditionalRuleSummary(fieldConfig.conditionalVisibility) : ''}
        </div>
        
        <!-- Validation Rules -->
        <div class="config-section">
          <h5 class="config-section-title">Validation Rules</h5>
          ${renderValidationConfig(fieldConfig)}
        </div>
      </div>
    </div>
  `;
}
```

#### **2.2 Conditional Rules Builder**

```javascript
function openConditionalRulesBuilder(blockId, sectionId, fieldId) {
  const modal = document.createElement('div');
  modal.className = 'modal-overlay';
  modal.innerHTML = `
    <div class="modal-content modal-large">
      <div class="modal-header">
        <h3>Conditional Visibility Rules</h3>
        <button class="btn-icon" onclick="closeModal()">✖</button>
      </div>
      
      <div class="modal-body">
        <div class="rules-builder">
          <div class="rules-group">
            <div class="rules-group-header">
              <select class="form-select" id="rule-logic">
                <option value="AND">All conditions must match (AND)</option>
                <option value="OR">Any condition can match (OR)</option>
              </select>
            </div>
            
            <div id="rules-list">
              <!-- Rules will be added here -->
            </div>
            
            <button class="btn btn-secondary btn-sm" onclick="addConditionalRule()">
              ➕ Add Condition
            </button>
          </div>
        </div>
        
        <div class="rule-template" style="display: none;">
          <div class="rule-item">
            <select class="form-select rule-field">
              <option value="">Select field...</option>
              ${getAllAvailableFields(blockId, sectionId).map(f => 
                `<option value="${f.fieldId}">${f.fieldLabel}</option>`
              ).join('')}
            </select>
            
            <select class="form-select rule-operator">
              <option value="equals">Equals</option>
              <option value="notEquals">Not Equals</option>
              <option value="contains">Contains</option>
              <option value="greaterThan">Greater Than</option>
              <option value="lessThan">Less Than</option>
              <option value="isEmpty">Is Empty</option>
              <option value="isNotEmpty">Is Not Empty</option>
            </select>
            
            <input type="text" class="form-input rule-value" placeholder="Value..." />
            
            <button class="btn-icon btn-danger" onclick="removeRule(this)">✖</button>
          </div>
        </div>
      </div>
      
      <div class="modal-footer">
        <button class="btn btn-secondary" onclick="closeModal()">Cancel</button>
        <button class="btn btn-primary" onclick="saveConditionalRules('${blockId}', '${sectionId}', '${fieldId}')">Save Rules</button>
      </div>
    </div>
  `;
  
  document.body.appendChild(modal);
}
```

---

### **Phase 3: Block-by-Block Implementation**

#### **3.1 General Information Block**

**Fields to Configure (25 total):**

1. Title (text, required)
2. Reference (text, readonly, auto-generated)
3. Sourcing or Solicitation (dropdown, triggers conditionals)
4. Sourcing Method (dropdown, conditional)
5. Type of Requirement (dropdown)
6. Description (textarea)
7. Beneficiary countries (multi-select)
8. Modality of Bid Receipt (dropdown, triggers waiver)
9. Waiver for Bid Receipt (text, conditional)
10. EPP (dropdown, triggers 3 fields)
11. EPP Authorization Link (text, conditional)
12. EPP Authorization End Date (date, conditional)
13. Scope of EPP Approval (dropdown, conditional)
14. Medicine/Medical Equipment (dropdown, triggers 1 field)
15. Policy Exception Link (text, conditional)
16. Process Type (checkbox-group, conditional, triggers 1 field)
17. Contract Amendment (dropdown, conditional)
18. Solicitation Method (dropdown, conditional)
19. Linked Process (search, complex conditional)
20. UN Collaboration (dropdown, triggers 2 fields)
21. UN Collaboration Type (dropdown, conditional)
22. UN Agencies (multi-select, conditional)
23. Organizational Unit (search)
24. Is this a Lease (dropdown, triggers 1 field)
25. SRM Compliance Document (file, conditional)

**Admin Configuration UI:**

```javascript
function renderGeneralInfoBlockConfig() {
  return `
    <div class="block-config">
      <div class="block-config-header">
        <h3>📋 General Information Block Configuration</h3>
        <div class="block-actions">
          <label class="checkbox-label">
            <input type="checkbox" checked onchange="toggleBlockEnabled('general-info', this.checked)" />
            Block Enabled
          </label>
        </div>
      </div>
      
      <div class="block-config-body">
        <div class="section-config">
          <div class="section-header">
            <h4>Basic Information Section</h4>
            <button class="btn btn-ghost btn-sm" onclick="addFieldToSection('general-info', 'basic-info')">
              ➕ Add Field
            </button>
          </div>
          
          <div class="fields-list" id="general-info-fields">
            ${renderFieldsList('general-info', 'basic-info')}
          </div>
        </div>
        
        <!-- Conditional Rules Summary -->
        <div class="conditional-rules-summary">
          <h4>Conditional Logic Overview</h4>
          <ul>
            <li><strong>Sourcing Method</strong> → Shows when "Sourcing" selected</li>
            <li><strong>Process Type, Solicitation Method</strong> → Show when "Solicitation" selected</li>
            <li><strong>Waiver field</strong> → Shows when "Email" or "Physical" submission</li>
            <li><strong>EPP fields (3)</strong> → Show when EPP = "Yes"</li>
            <li><strong>Policy Exception</strong> → Shows when Medicine = "Yes"</li>
            <li><strong>Contract Amendment</strong> → Shows when "Direct contracting" checked</li>
            <li><strong>UN fields (2)</strong> → Show when UN Collaboration = "Yes"</li>
            <li><strong>SRM Document</strong> → Shows when Lease = "Yes"</li>
          </ul>
        </div>
      </div>
    </div>
  `;
}
```

#### **3.2 Project & Tender Details Block**

**Project Information Section:**
- 4 search fields from oneUNOPS

**Particulars Section (63 fields):**

Admin needs to configure all 63 fields across 10 subsections.

**Example: Pre-Bid Meetings Configuration:**

```javascript
function renderPreBidMeetingsConfig() {
  return `
    <div class="subsection-config">
      <div class="subsection-header">
        <h5>Pre-Bid Meetings Configuration</h5>
      </div>
      
      <div class="subsection-body">
        <!-- Trigger Field -->
        <div class="field-config-card">
          <h6>Clarifications or pre-bid meeting</h6>
          <div class="form-group">
            <label>Field Type</label>
            <select class="form-select">
              <option value="dropdown" selected>Dropdown</option>
            </select>
          </div>
          <div class="form-group">
            <label>Options</label>
            <div class="options-list">
              <input type="text" class="form-input" value="No pre-bid meeting" readonly />
              <input type="text" class="form-input" value="One pre-bid meeting" readonly />
              <input type="text" class="form-input" value="Two pre-bid meetings" readonly />
            </div>
          </div>
        </div>
        
        <!-- Conditional Fields Configuration -->
        <div class="conditional-fields-config">
          <h6>When "One pre-bid meeting" or "Two pre-bid meetings" is selected:</h6>
          
          <div class="repeating-group-config">
            <h6>Meeting Fields (repeats per meeting)</h6>
            
            <div class="field-config-mini">
              <label>Date and time for pre-bid meeting</label>
              <select class="form-select">
                <option value="datetime-local" selected>Date & Time</option>
              </select>
              <label class="checkbox-label">
                <input type="checkbox" />
                Required
              </label>
            </div>
            
            <div class="field-config-mini">
              <label>Type of pre-bid meeting</label>
              <select class="form-select">
                <option value="dropdown" selected>Dropdown</option>
              </select>
              <div class="options-mini">
                <span class="badge">In-person</span>
                <span class="badge">Virtual</span>
                <span class="badge">Hybrid</span>
              </div>
            </div>
            
            <div class="field-config-mini">
              <label>Pre-bid meeting details</label>
              <select class="form-select">
                <option value="textarea" selected>Textarea</option>
              </select>
            </div>
          </div>
        </div>
      </div>
    </div>
  `;
}
```

#### **3.3 Requisition & Lots Block**

**Admin Configuration:**

```javascript
function renderRequisitionLotsConfig() {
  return `
    <div class="block-config">
      <div class="block-config-header">
        <h3>📋 Requisition & Lots Block Configuration</h3>
      </div>
      
      <div class="block-config-body">
        <div class="config-section">
          <h4>Requisition Lines Configuration</h4>
          
          <div class="form-group">
            <label>Data Source</label>
            <select class="form-select">
              <option value="oneUNOPS-ERP" selected>oneUNOPS ERP</option>
              <option value="manual-entry">Manual Entry</option>
              <option value="upload-spreadsheet">Upload Spreadsheet</option>
            </select>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Display requisition lines table
            </label>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Enable lot creation
            </label>
          </div>
        </div>
        
        <div class="config-section">
          <h4>Lot Configuration</h4>
          
          <div class="form-group">
            <label>Lot Assignment Rules</label>
            <select class="form-select">
              <option value="manual" selected>Manual assignment by tender author</option>
              <option value="auto-by-category">Auto-assign by UNSPSC category</option>
              <option value="auto-by-value">Auto-assign by estimated value</option>
            </select>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Allow adding UNSPSC codes not in requisition lines
            </label>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Require quantity per UNSPSC code
            </label>
          </div>
        </div>
      </div>
    </div>
  `;
}
```

#### **3.4 Scope & Requirements Block**

**Product Requirements & Specifications:**

Admin configures the two mutually exclusive modes:

```javascript
function renderScopeRequirementsConfig() {
  return `
    <div class="block-config">
      <div class="block-config-header">
        <h3>🎯 Scope & Requirements Block Configuration</h3>
      </div>
      
      <div class="block-config-body">
        <div class="config-section">
          <h4>Product Requirements & Specifications</h4>
          
          <div class="form-group">
            <label>Requirement Input Mode</label>
            <div class="radio-group">
              <label class="radio-label">
                <input type="radio" name="req-mode" value="choice" checked />
                Allow tender author to choose (Attributes OR Description)
              </label>
              <label class="radio-label">
                <input type="radio" name="req-mode" value="attributes-only" />
                Force Attributes mode only
              </label>
              <label class="radio-label">
                <input type="radio" name="req-mode" value="description-only" />
                Force Description mode only
              </label>
            </div>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Enable spreadsheet export/upload
            </label>
          </div>
          
          <div class="subsection-config">
            <h5>Attributes Mode Configuration</h5>
            <div class="form-group">
              <label>Attribute Fields</label>
              <div class="fields-list">
                <div class="field-item">
                  <span>Attribute Name</span>
                  <span class="badge">Text Input</span>
                  <span class="badge badge-success">Required</span>
                </div>
                <div class="field-item">
                  <span>Attribute Value</span>
                  <span class="badge">Text Input</span>
                  <span class="badge badge-success">Required</span>
                </div>
              </div>
            </div>
          </div>
          
          <div class="subsection-config">
            <h5>Description Mode Configuration</h5>
            <div class="form-group">
              <label class="checkbox-label">
                <input type="checkbox" checked />
                Enable rich text editor
              </label>
            </div>
            <div class="form-group">
              <label>Enabled Formatting Tools</label>
              <div class="checkbox-group">
                <label class="checkbox-label"><input type="checkbox" checked /> Bold</label>
                <label class="checkbox-label"><input type="checkbox" checked /> Italic</label>
                <label class="checkbox-label"><input type="checkbox" checked /> Underline</label>
                <label class="checkbox-label"><input type="checkbox" checked /> Lists</label>
                <label class="checkbox-label"><input type="checkbox" checked /> Headings</label>
              </div>
            </div>
          </div>
        </div>
        
        <div class="config-section">
          <h4>Tender Author Configuration</h4>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Enable Service Descriptions
            </label>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Enable Delivery Requirements (with add/delete fields)
            </label>
          </div>
          
          <div class="form-group">
            <label>Default Delivery Requirement Fields</label>
            <div class="fields-list editable-list">
              <div class="list-item">
                <span>Delivery Time</span>
                <button class="btn-icon" onclick="editDeliveryField('deliveryTime')">✏️</button>
                <button class="btn-icon" onclick="removeDeliveryField('deliveryTime')">✖</button>
              </div>
              <div class="list-item">
                <span>Delivery Place</span>
                <button class="btn-icon" onclick="editDeliveryField('deliveryPlace')">✏️</button>
                <button class="btn-icon" onclick="removeDeliveryField('deliveryPlace')">✖</button>
              </div>
              <div class="list-item">
                <span>Allowed Incoterms</span>
                <button class="btn-icon" onclick="editDeliveryField('allowedIncoterms')">✏️</button>
                <button class="btn-icon" onclick="removeDeliveryField('allowedIncoterms')">✖</button>
              </div>
              <div class="list-item">
                <span>Consignee Details</span>
                <button class="btn-icon" onclick="editDeliveryField('consigneeDetails')">✏️</button>
                <button class="btn-icon" onclick="removeDeliveryField('consigneeDetails')">✖</button>
              </div>
            </div>
            <button class="btn btn-secondary btn-sm" onclick="addDeliveryField()">➕ Add Field</button>
          </div>
        </div>
      </div>
    </div>
  `;
}
```

#### **3.5 Submission Requirements Block**

**Bid Response Requirements:**

```javascript
function renderSubmissionRequirementsConfig() {
  return `
    <div class="block-config">
      <div class="block-config-header">
        <h3>📝 Submission Requirements Block Configuration</h3>
      </div>
      
      <div class="block-config-body">
        <div class="config-section">
          <h4>Bid Response Requirements</h4>
          
          <p class="help-text">
            Configure the default fields for each response level. Tender authors can add/remove fields during authoring.
          </p>
          
          ${['perBid', 'perUNSPSC', 'perAttribute', 'perServiceLine', 'perDelivery'].map(section => `
            <div class="subsection-config">
              <h5>${getSectionDisplayName(section)}</h5>
              
              <div class="form-group">
                <label class="checkbox-label">
                  <input type="checkbox" checked onchange="toggleSection('${section}', this.checked)" />
                  Enable this section
                </label>
              </div>
              
              <div class="form-group">
                <label>Default Fields</label>
                <div class="fields-list editable-list" id="${section}-fields">
                  ${getDefaultFieldsForSection(section).map(field => `
                    <div class="list-item">
                      <span>${field.name}</span>
                      <span class="badge">${field.type}</span>
                      <button class="btn-icon" onclick="editBidResponseField('${section}', '${field.id}')">✏️</button>
                      <button class="btn-icon" onclick="removeBidResponseField('${section}', '${field.id}')">✖</button>
                    </div>
                  `).join('')}
                </div>
                <button class="btn btn-secondary btn-sm" onclick="addBidResponseField('${section}')">
                  ➕ Add Field
                </button>
              </div>
              
              <div class="form-group">
                <label class="checkbox-label">
                  <input type="checkbox" checked />
                  Allow tender author to add custom fields
                </label>
              </div>
              
              <div class="form-group">
                <label class="checkbox-label">
                  <input type="checkbox" checked />
                  Allow tender author to remove default fields
                </label>
              </div>
            </div>
          `).join('')}
        </div>
        
        <div class="config-section">
          <h4>Supplier Form Preview</h4>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Enable supplier form preview
            </label>
          </div>
          
          <div class="form-group">
            <label>Preview Display Mode</label>
            <select class="form-select">
              <option value="toggle" selected>Toggleable (with eye icon)</option>
              <option value="always-visible">Always visible</option>
              <option value="hidden">Hidden</option>
            </select>
          </div>
        </div>
      </div>
    </div>
  `;
}
```

#### **3.6 Evaluation Block**

**Evaluation Method & Criteria:**

```javascript
function renderEvaluationBlockConfig() {
  return `
    <div class="block-config">
      <div class="block-config-header">
        <h3>📊 Evaluation Block Configuration</h3>
      </div>
      
      <div class="block-config-body">
        <div class="config-section">
          <h4>Evaluation Method Section</h4>
          
          <div class="form-group">
            <label>Evaluation method field</label>
            <select class="form-select">
              <option value="dropdown" selected>Dropdown</option>
              <option value="radio">Radio buttons</option>
            </select>
          </div>
          
          <div class="form-group">
            <label>Available Options</label>
            <div class="options-list editable-list">
              <div class="list-item">
                <span>Lowest price</span>
                <button class="btn-icon" onclick="editOption('evalMethod', 'lowestPrice')">✏️</button>
              </div>
              <div class="list-item">
                <span>Cumulative analysis</span>
                <button class="btn-icon" onclick="editOption('evalMethod', 'cumulative')">✏️</button>
                <span class="badge">Triggers: Weightage, Min Points</span>
              </div>
              <div class="list-item">
                <span>Two-stage</span>
                <button class="btn-icon" onclick="editOption('evalMethod', 'twoStage')">✏️</button>
              </div>
            </div>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Show conditional fields for Cumulative analysis
            </label>
          </div>
        </div>
        
        <div class="config-section">
          <h4>Evaluation Criteria Section</h4>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Enable evaluation criteria management
            </label>
          </div>
          
          <div class="form-group">
            <label>Criterion Types</label>
            <div class="options-list editable-list">
              <div class="list-item">
                <span>Eligibility and formal criteria</span>
                <input type="checkbox" checked />
              </div>
              <div class="list-item">
                <span>Qualification criteria</span>
                <input type="checkbox" checked />
              </div>
              <div class="list-item">
                <span>Technical criteria</span>
                <input type="checkbox" checked />
              </div>
              <div class="list-item">
                <span>Shortlisting criteria</span>
                <input type="checkbox" checked />
              </div>
            </div>
          </div>
          
          <div class="form-group">
            <label>Evaluation Methods</label>
            <div class="options-list editable-list">
              <div class="list-item">
                <span>Pass/Fail</span>
                <input type="checkbox" checked />
              </div>
              <div class="list-item">
                <span>Scoring (0-100)</span>
                <input type="checkbox" checked />
              </div>
              <div class="list-item">
                <span>Weighted Scoring</span>
                <input type="checkbox" checked />
              </div>
              <div class="list-item">
                <span>Ranking</span>
                <input type="checkbox" checked />
              </div>
            </div>
          </div>
          
          <div class="form-group">
            <label class="checkbox-label">
              <input type="checkbox" checked />
              Allow UNSPSC-specific targeting
            </label>
          </div>
        </div>
      </div>
    </div>
  `;
}
```

#### **3.7 Team Block**

```javascript
function renderTeamBlockConfig() {
  return `
    <div class="block-config">
      <div class="block-config-header">
        <h3>👥 Team Block Configuration</h3>
      </div>
      
      <div class="block-config-body">
        <div class="config-section">
          <h4>Team Member Fields</h4>
          
          <p class="help-text">
            All team fields are search fields sourced from oneUNOPS RDAT.
          </p>
          
          ${[
            { id: 'alternateAuthors', label: 'Alternate authors', multiSelect: true },
            { id: 'procurementReviewer', label: 'Procurement Reviewer', multiSelect: false },
            { id: 'procurementAuthority', label: 'Procurement Authority', multiSelect: false, prefilled: true },
            { id: 'technicalExpert', label: 'Technical Matter Expert', multiSelect: false },
            { id: 'bidOpeningPanel', label: 'Bid opening panel', multiSelect: false, conditional: 'public bid openings' },
            { id: 'evaluationTeam', label: 'Evaluation team', multiSelect: true },
            { id: 'collaborators', label: 'Collaborators', multiSelect: true }
          ].map(field => `
            <div class="field-config-card">
              <div class="field-config-header">
                <h5>${field.label}</h5>
                <label class="checkbox-label">
                  <input type="checkbox" checked />
                  Enabled
                </label>
              </div>
              
              <div class="field-config-body">
                <div class="form-group">
                  <label>Data Source</label>
                  <input type="text" class="form-input" value="oneUNOPS RDAT" readonly />
                </div>
                
                <div class="form-group">
                  <label class="checkbox-label">
                    <input type="checkbox" ${field.multiSelect ? 'checked' : ''} />
                    Allow multiple selections
                  </label>
                </div>
                
                <div class="form-group">
                  <label class="checkbox-label">
                    <input type="checkbox" ${field.required ? 'checked' : ''} />
                    Required field
                  </label>
                </div>
                
                ${field.prefilled ? `
                  <div class="form-group">
                    <label class="checkbox-label">
                      <input type="checkbox" checked />
                      Pre-filled from system
                    </label>
                  </div>
                ` : ''}
                
                ${field.conditional ? `
                  <div class="form-group">
                    <label>Conditional Visibility</label>
                    <input type="text" class="form-input" value="Required for ${field.conditional}" readonly />
                  </div>
                ` : ''}
              </div>
            </div>
          `).join('')}
        </div>
      </div>
    </div>
  `;
}
```

---

### **Phase 4: Conditional Logic Engine**

#### **4.1 Rule Evaluation Engine**

```javascript
class ConditionalLogicEngine {
  constructor(templateConfig) {
    this.config = templateConfig;
    this.currentValues = {};
  }
  
  evaluateRule(rule) {
    const { field, operator, value } = rule.condition;
    const currentValue = this.currentValues[field];
    
    switch (operator) {
      case 'equals':
        return currentValue === value;
      case 'notEquals':
        return currentValue !== value;
      case 'contains':
        return Array.isArray(currentValue) ? currentValue.includes(value) : currentValue?.includes(value);
      case 'greaterThan':
        return parseFloat(currentValue) > parseFloat(value);
      case 'lessThan':
        return parseFloat(currentValue) < parseFloat(value);
      case 'isEmpty':
        return !currentValue || currentValue === '';
      case 'isNotEmpty':
        return !!currentValue && currentValue !== '';
      default:
        return false;
    }
  }
  
  evaluateRuleGroup(ruleGroup) {
    const { logic, rules } = ruleGroup;
    
    if (logic === 'AND') {
      return rules.every(rule => this.evaluateRule(rule));
    } else if (logic === 'OR') {
      return rules.some(rule => this.evaluateRule(rule));
    }
    
    return false;
  }
  
  getVisibleFields(blockId, sectionId) {
    const section = this.config.blocks
      .find(b => b.blockId === blockId)
      ?.sections.find(s => s.sectionId === sectionId);
    
    if (!section) return [];
    
    return section.fields.filter(field => {
      if (!field.conditionalVisibility) return true;
      return this.evaluateRuleGroup(field.conditionalVisibility);
    });
  }
  
  updateValue(fieldId, value) {
    this.currentValues[fieldId] = value;
    // Trigger re-evaluation of conditional fields
    this.reevaluateConditionals();
  }
  
  reevaluateConditionals() {
    // Re-render fields based on updated conditional logic
    this.config.blocks.forEach(block => {
      block.sections.forEach(section => {
        const visibleFields = this.getVisibleFields(block.blockId, section.sectionId);
        // Update UI to show/hide fields
        this.updateSectionUI(block.blockId, section.sectionId, visibleFields);
      });
    });
  }
}
```

---

### **Phase 5: Preview Mode**

#### **5.1 Template Preview**

Admin should be able to preview how the template will look for tender authors:

```javascript
function openTemplatePreview() {
  const modal = document.createElement('div');
  modal.className = 'modal-overlay modal-fullscreen';
  modal.innerHTML = `
    <div class="modal-content modal-xl">
      <div class="modal-header">
        <h3>Template Preview - Tender Author View</h3>
        <button class="btn-icon" onclick="closeModal()">✖</button>
      </div>
      
      <div class="modal-body">
        <div class="preview-container">
          <div class="preview-sidebar">
            <h4>Preview Navigation</h4>
            <ul class="preview-nav">
              ${templateConfig.blocks.filter(b => b.enabled).map(block => `
                <li>
                  <a href="#preview-${block.blockId}">${block.blockIcon} ${block.blockName}</a>
                </li>
              `).join('')}
            </ul>
          </div>
          
          <div class="preview-content">
            ${templateConfig.blocks.filter(b => b.enabled).map(block => `
              <div id="preview-${block.blockId}" class="preview-block">
                <h3>${block.blockIcon} ${block.blockName}</h3>
                ${renderPreviewBlock(block)}
              </div>
            `).join('')}
          </div>
        </div>
      </div>
      
      <div class="modal-footer">
        <button class="btn btn-primary" onclick="closeModal()">Close Preview</button>
      </div>
    </div>
  `;
  
  document.body.appendChild(modal);
}

function renderPreviewBlock(block) {
  return block.sections.map(section => `
    <div class="preview-section">
      <h4>${section.sectionName}</h4>
      <div class="form-grid">
        ${section.fields.filter(f => f.enabled).map(field => renderPreviewField(field)).join('')}
      </div>
    </div>
  `).join('');
}

function renderPreviewField(field) {
  const requiredMark = field.required ? '<span class="required">*</span>' : '';
  
  switch (field.fieldType) {
    case 'text':
      return `
        <div class="form-group">
          <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
          <input type="text" class="form-input" placeholder="${field.placeholder || ''}" ${field.required ? 'required' : ''} />
          ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
        </div>
      `;
    case 'textarea':
      return `
        <div class="form-group full-width">
          <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
          <textarea class="form-textarea" placeholder="${field.placeholder || ''}" ${field.required ? 'required' : ''}></textarea>
          ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
        </div>
      `;
    case 'dropdown':
      return `
        <div class="form-group">
          <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
          <select class="form-select" ${field.required ? 'required' : ''}>
            <option value="">Select...</option>
            ${field.options?.map(opt => `<option value="${opt}">${opt}</option>`).join('') || ''}
          </select>
          ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
        </div>
      `;
    // ... other field types
    default:
      return '';
  }
}
```

---

### **Phase 6: Export/Import**

#### **6.1 Template Export**

```javascript
function exportTemplate() {
  const templateJSON = JSON.stringify(templateConfig, null, 2);
  const blob = new Blob([templateJSON], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  
  const a = document.createElement('a');
  a.href = url;
  a.download = `${templateConfig.templateName.replace(/\s+/g, '-').toLowerCase()}-template.json`;
  a.click();
  
  URL.revokeObjectURL(url);
}
```

#### **6.2 Template Import**

```javascript
function importTemplate(file) {
  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const imported = JSON.parse(e.target.result);
      // Validate structure
      if (validateTemplateStructure(imported)) {
        templateConfig = imported;
        updateCanvasView();
        alert('Template imported successfully!');
      } else {
        alert('Invalid template structure');
      }
    } catch (error) {
      alert('Error parsing template file: ' + error.message);
    }
  };
  reader.readAsText(file);
}

function validateTemplateStructure(template) {
  // Validate required properties
  return template &&
         template.templateId &&
         template.templateName &&
         Array.isArray(template.blocks) &&
         template.blocks.every(block => 
           block.blockId &&
           block.blockName &&
           Array.isArray(block.sections)
         );
}
```

---

## Implementation Timeline

### **Week 1-2: Foundation**
- Phase 1: Data structure implementation
- Phase 2: Core UI components
- Basic field configuration panels

### **Week 3-4: Block Implementation**
- Phase 3.1: General Information Block
- Phase 3.2: Project & Tender Details Block
- Phase 3.3: Requisition & Lots Block

### **Week 5-6: Advanced Blocks**
- Phase 3.4: Scope & Requirements Block
- Phase 3.5: Submission Requirements Block
- Phase 3.6: Evaluation Block
- Phase 3.7: Team Block

### **Week 7: Logic & Preview**
- Phase 4: Conditional logic engine
- Phase 5: Preview mode

### **Week 8: Polish**
- Phase 6: Export/Import
- Testing and bug fixes
- Documentation

---

## Key Implementation Patterns

### **Pattern 1: Field Configuration Component**

Every field type has a standard configuration interface:
- Label, Type, Required, Enabled
- Default value, Placeholder, Help text
- Conditional visibility rules
- Validation rules

### **Pattern 2: Conditional Visibility**

All conditional logic is configured through a visual rules builder:
- Select trigger field
- Choose operator
- Set value
- Define AND/OR logic

### **Pattern 3: Repeating Groups**

For sections with repeating data (pre-bid meetings, evaluation criteria):
- Configure once, repeat dynamically
- Set minimum/maximum instances
- Define field structure

### **Pattern 4: Preview Mode**

Admin can always preview the tender author view:
- Switch between admin config and preview
- Test conditional logic
- Verify field behavior

---

## Next Steps

This is a comprehensive plan. To implement:

1. **Start with Phase 1**: Implement the data structure
2. **Build Phase 2**: Create reusable UI components
3. **Implement blocks sequentially**: One block at a time (Phase 3)
4. **Add logic**: Conditional rules engine (Phase 4)
5. **Enable preview**: Preview mode (Phase 5)
6. **Add export/import**: Template portability (Phase 6)

Each phase can be implemented incrementally and tested independently.

---

## Conclusion

This enhancement will transform `template-builder-workspace.html` into a comprehensive admin configuration tool that mirrors all capabilities of `tender-create.html` while providing powerful template configuration features.

The admin will be able to:
- Configure every field from tender-create
- Set conditional visibility rules
- Define validation requirements
- Preview how templates appear to tender authors
- Export/import templates for reuse

This creates a complete template management system for the UNOPS procurement platform.

