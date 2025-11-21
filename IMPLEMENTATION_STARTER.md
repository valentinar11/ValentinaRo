# Template Builder Workspace - Implementation Starter Guide

## Quick Start

This guide provides **working code examples** for implementing the template builder enhancement. Use these patterns to replicate across all blocks.

---

## Core Data Structure

### Add to `template-builder-workspace.html` (inside `<script>` tag)

```javascript
// ===== TEMPLATE CONFIGURATION DATA STRUCTURE =====

let templateConfig = {
  templateId: generateId(),
  templateName: 'Untitled Template',
  version: '1.0',
  createdDate: new Date().toISOString(),
  modifiedDate: new Date().toISOString(),
  blocks: []
};

// Field type definitions
const fieldTypes = {
  text: { icon: '📝', label: 'Text Input' },
  textarea: { icon: '📄', label: 'Textarea' },
  dropdown: { icon: '▼', label: 'Dropdown' },
  checkbox: { icon: '☑️', label: 'Checkbox' },
  checkboxGroup: { icon: '☑️', label: 'Checkbox Group' },
  radio: { icon: '🔘', label: 'Radio Buttons' },
  date: { icon: '📅', label: 'Date' },
  datetime: { icon: '🕐', label: 'Date & Time' },
  number: { icon: '🔢', label: 'Number' },
  search: { icon: '🔍', label: 'Search Field' },
  file: { icon: '📎', label: 'File Upload' },
  richText: { icon: '✒️', label: 'Rich Text Editor' }
};

// Block definitions (same structure as tender-create.html)
const availableBlocks = [
  {
    id: 'general-info',
    name: 'General Information',
    icon: '📋',
    description: 'Basic tender information and procurement details'
  },
  {
    id: 'project',
    name: 'Project & Tender Details',
    icon: '🏗️',
    description: 'Project information and tender particulars'
  },
  {
    id: 'requisition-lots',
    name: 'Requisition & Lots',
    icon: '📦',
    description: 'Requisition lines and lot management'
  },
  {
    id: 'scope',
    name: 'Scope & Requirements',
    icon: '🎯',
    description: 'Product requirements and specifications'
  },
  {
    id: 'submission',
    name: 'Submission Requirements',
    icon: '📝',
    description: 'Supplier response structure'
  },
  {
    id: 'evaluation',
    name: 'Evaluation',
    icon: '📊',
    description: 'Evaluation method and criteria'
  },
  {
    id: 'team',
    name: 'Team',
    icon: '👥',
    description: 'Team members and roles'
  }
];

// Helper function to generate unique IDs
function generateId() {
  return 'id-' + Date.now() + '-' + Math.random().toString(36).substr(2, 9);
}
```

---

## Example Implementation: General Information Block

### Complete Working Example

```javascript
// ===== GENERAL INFORMATION BLOCK - FULL IMPLEMENTATION =====

// 1. Block initialization when dropped on canvas
function initializeGeneralInfoBlock() {
  const blockConfig = {
    blockId: 'general-info-' + generateId(),
    blockType: 'general-info',
    blockName: 'General Information',
    blockIcon: '📋',
    enabled: true,
    position: templateConfig.blocks.length,
    sections: [
      {
        sectionId: 'basic-info',
        sectionName: 'Basic Information',
        enabled: true,
        fields: [
          createFieldConfig('title', 'Title', 'text', true, {
            placeholder: 'Enter tender title',
            helpText: 'Provide a clear, descriptive title for the tender',
            validation: { minLength: 10, maxLength: 200 }
          }),
          createFieldConfig('reference', 'Reference', 'text', false, {
            placeholder: 'Auto-generated',
            helpText: 'Unique tender reference number',
            readonly: true
          }),
          createFieldConfig('sourcingOrSolicitation', 'Sourcing or Solicitation', 'dropdown', true, {
            options: ['Sourcing', 'Solicitation'],
            triggersConditionalFields: ['sourcingMethod', 'processType', 'solicitationMethod']
          }),
          createFieldConfig('sourcingMethod', 'Sourcing Method', 'dropdown', false, {
            options: ['RFI', 'EOI', 'PQ', 'Ad-hoc shortlist'],
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'sourcingOrSolicitation', operator: 'equals', value: 'Sourcing' }]
            }
          }),
          createFieldConfig('typeOfRequirement', 'Type of Requirement', 'dropdown', true, {
            options: ['Goods', 'Services', 'Works', 'Goods & Services']
          }),
          createFieldConfig('description', 'Description', 'textarea', true, {
            placeholder: 'Provide a detailed description',
            rows: 4
          }),
          createFieldConfig('beneficiaryCountries', 'Beneficiary Countries', 'dropdown', false, {
            allowMultiple: true,
            options: [] // Would be populated from country list
          }),
          createFieldConfig('modalityOfBidReceipt', 'Modality of Bid Receipt', 'dropdown', true, {
            options: ['Online Only', 'Email Submission', 'Physical Submission'],
            triggersConditionalFields: ['waiverForBidReceipt']
          }),
          createFieldConfig('waiverForBidReceipt', 'Waiver for Bid Receipt Modality', 'text', false, {
            placeholder: 'Provide waiver justification link',
            conditionalVisibility: {
              logic: 'OR',
              rules: [
                { field: 'modalityOfBidReceipt', operator: 'equals', value: 'Email Submission' },
                { field: 'modalityOfBidReceipt', operator: 'equals', value: 'Physical Submission' }
              ]
            }
          }),
          createFieldConfig('epp', 'EPP', 'dropdown', false, {
            options: ['Yes', 'No'],
            triggersConditionalFields: ['eppAuthorizationLink', 'eppEndDate', 'eppScope']
          }),
          createFieldConfig('eppAuthorizationLink', 'EPP Authorization (Jira Link)', 'text', false, {
            placeholder: 'Enter Jira link',
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'epp', operator: 'equals', value: 'Yes' }]
            }
          }),
          createFieldConfig('eppEndDate', 'EPP Authorization End Date', 'date', false, {
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'epp', operator: 'equals', value: 'Yes' }]
            }
          }),
          createFieldConfig('eppScope', 'Scope of EPP Approval', 'dropdown', false, {
            options: ['Project', 'Org Unit', 'Specific procurement process', 'Other'],
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'epp', operator: 'equals', value: 'Yes' }]
            }
          }),
          createFieldConfig('medicineMedicalEquipment', 'Medicine / Medical Equipment', 'dropdown', false, {
            options: ['Yes', 'No'],
            triggersConditionalFields: ['policyExceptionLink']
          }),
          createFieldConfig('policyExceptionLink', 'Policy Exception Link (Annex 2 Jira)', 'text', false, {
            placeholder: 'Enter Jira link',
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'medicineMedicalEquipment', operator: 'equals', value: 'Yes' }]
            }
          }),
          createFieldConfig('processType', 'Process Type', 'checkboxGroup', false, {
            options: [
              'Standard solicitation',
              'Solicit offers against LTA/BPA',
              'Secondary bidding against LTA/BPA',
              'Direct contracting / Sole sourcing',
              'Contract Amendment'
            ],
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'sourcingOrSolicitation', operator: 'equals', value: 'Solicitation' }]
            }
          }),
          createFieldConfig('solicitationMethod', 'Solicitation Method', 'dropdown', false, {
            options: ['RFQ', 'ITB', 'RFP', 'e-reserve auctions'],
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'sourcingOrSolicitation', operator: 'equals', value: 'Solicitation' }]
            }
          }),
          createFieldConfig('linkedProcess', 'Linked Process', 'search', false, {
            dataSource: 'Event ID',
            placeholder: 'Search for event...',
            conditionalVisibility: {
              logic: 'OR',
              rules: [
                { field: 'processType', operator: 'contains', value: 'Direct contracting / Sole sourcing' },
                { field: 'sourcingOrSolicitation', operator: 'isNotEmpty', value: null },
                { field: 'processType', operator: 'contains', value: 'Solicit offers against LTA/BPA' },
                { field: 'processType', operator: 'contains', value: 'Contract Amendment' }
              ]
            }
          }),
          createFieldConfig('unCollaboration', 'UN Collaboration', 'dropdown', false, {
            options: ['Yes', 'No'],
            triggersConditionalFields: ['unCollaborationType', 'unAgencies']
          }),
          createFieldConfig('unCollaborationType', 'UN Collaboration Type', 'dropdown', false, {
            options: [
              'Joint tendering process with other UN organizations',
              'Secondary bidding using other UN agency\'s LTA',
              'Re-use of solicitation results of another UN agency'
            ],
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'unCollaboration', operator: 'equals', value: 'Yes' }]
            }
          }),
          createFieldConfig('unAgencies', 'UN Agencies', 'dropdown', false, {
            allowMultiple: true,
            options: ['UNDP', 'UNHCR', 'UNICEF', 'WFP', 'WHO', 'UNESCO'],
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'unCollaboration', operator: 'equals', value: 'Yes' }]
            }
          }),
          createFieldConfig('organizationalUnit', 'Organizational Unit', 'search', true, {
            dataSource: 'oneUNOPS',
            placeholder: 'Search for organizational unit...'
          }),
          createFieldConfig('isLease', 'Is this a Lease', 'dropdown', false, {
            options: ['Yes', 'No'],
            triggersConditionalFields: ['srmComplianceDoc']
          }),
          createFieldConfig('srmComplianceDoc', 'SRM Compliance Document', 'file', false, {
            acceptedTypes: ['.pdf', '.doc', '.docx'],
            maxSize: 10,
            conditionalVisibility: {
              logic: 'AND',
              rules: [{ field: 'isLease', operator: 'equals', value: 'Yes' }]
            }
          })
        ]
      }
    ]
  };
  
  return blockConfig;
}

// 2. Helper function to create field configuration
function createFieldConfig(fieldId, label, type, required = false, options = {}) {
  return {
    fieldId: fieldId,
    fieldLabel: label,
    fieldType: type,
    required: required,
    enabled: true,
    defaultValue: options.defaultValue || '',
    placeholder: options.placeholder || '',
    helpText: options.helpText || '',
    options: options.options || [],
    allowMultiple: options.allowMultiple || false,
    dataSource: options.dataSource || null,
    acceptedTypes: options.acceptedTypes || [],
    maxSize: options.maxSize || null,
    rows: options.rows || 3,
    readonly: options.readonly || false,
    validation: options.validation || {},
    conditionalVisibility: options.conditionalVisibility || null,
    triggersConditionalFields: options.triggersConditionalFields || [],
    position: 0
  };
}

// 3. Render function for admin configuration view
function renderBlockConfigView(blockConfig) {
  return `
    <div class="block-config-container" id="config-${blockConfig.blockId}">
      <div class="block-config-header">
        <div class="block-config-title">
          <span class="block-icon">${blockConfig.blockIcon}</span>
          <input type="text" class="block-name-input" value="${blockConfig.blockName}" 
                 onchange="updateBlockName('${blockConfig.blockId}', this.value)" />
        </div>
        <div class="block-config-actions">
          <label class="toggle-label">
            <input type="checkbox" ${blockConfig.enabled ? 'checked' : ''} 
                   onchange="toggleBlockEnabled('${blockConfig.blockId}', this.checked)" />
            <span>Block Enabled</span>
          </label>
          <button class="btn-icon" onclick="duplicateBlock('${blockConfig.blockId}')" title="Duplicate Block">📋</button>
          <button class="btn-icon" onclick="deleteBlock('${blockConfig.blockId}')" title="Delete Block">🗑️</button>
        </div>
      </div>
      
      ${blockConfig.sections.map(section => renderSectionConfigView(blockConfig.blockId, section)).join('')}
      
      <div class="block-config-footer">
        <button class="btn btn-secondary" onclick="addSection('${blockConfig.blockId}')">
          ➕ Add Section
        </button>
      </div>
    </div>
  `;
}

// 4. Render function for section configuration
function renderSectionConfigView(blockId, section) {
  return `
    <div class="section-config" id="section-${section.sectionId}">
      <div class="section-config-header">
        <input type="text" class="section-name-input" value="${section.sectionName}" 
               onchange="updateSectionName('${blockId}', '${section.sectionId}', this.value)" />
        <div class="section-actions">
          <label class="toggle-label-sm">
            <input type="checkbox" ${section.enabled ? 'checked' : ''} 
                   onchange="toggleSectionEnabled('${blockId}', '${section.sectionId}', this.checked)" />
            <span>Enabled</span>
          </label>
          <button class="btn-icon-sm" onclick="deleteSection('${blockId}', '${section.sectionId}')">✖</button>
        </div>
      </div>
      
      <div class="fields-config-list">
        ${section.fields.map(field => renderFieldConfigCard(blockId, section.sectionId, field)).join('')}
      </div>
      
      <button class="btn btn-ghost btn-sm" onclick="addFieldToSection('${blockId}', '${section.sectionId}')">
        ➕ Add Field
      </button>
    </div>
  `;
}

// 5. Render function for individual field configuration card
function renderFieldConfigCard(blockId, sectionId, field) {
  return `
    <div class="field-config-card ${!field.enabled ? 'disabled' : ''}" id="field-${field.fieldId}">
      <div class="field-config-card-header">
        <div class="field-info">
          <span class="field-type-badge">${fieldTypes[field.fieldType]?.icon || '📝'}</span>
          <span class="field-label">${field.fieldLabel}</span>
          ${field.required ? '<span class="badge badge-required">Required</span>' : ''}
          ${field.conditionalVisibility ? '<span class="badge badge-conditional">Conditional</span>' : ''}
        </div>
        <div class="field-actions">
          <button class="btn-icon-sm" onclick="editField('${blockId}', '${sectionId}', '${field.fieldId}')" title="Edit">✏️</button>
          <button class="btn-icon-sm" onclick="duplicateField('${blockId}', '${sectionId}', '${field.fieldId}')" title="Duplicate">📋</button>
          <button class="btn-icon-sm" onclick="deleteField('${blockId}', '${sectionId}', '${field.fieldId}')" title="Delete">🗑️</button>
        </div>
      </div>
      
      <div class="field-config-card-body">
        <div class="field-config-preview">
          ${renderFieldPreview(field)}
        </div>
      </div>
    </div>
  `;
}

// 6. Render field preview (how it will look for tender author)
function renderFieldPreview(field) {
  const requiredMark = field.required ? '<span class="required">*</span>' : '';
  
  switch (field.fieldType) {
    case 'text':
      return `
        <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
        <input type="text" class="form-input" placeholder="${field.placeholder}" disabled />
        ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
      `;
    
    case 'textarea':
      return `
        <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
        <textarea class="form-textarea" placeholder="${field.placeholder}" rows="${field.rows}" disabled></textarea>
        ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
      `;
    
    case 'dropdown':
      return `
        <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
        <select class="form-select" disabled ${field.allowMultiple ? 'multiple' : ''}>
          <option value="">Select...</option>
          ${field.options.map(opt => `<option value="${opt}">${opt}</option>`).join('')}
        </select>
        ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
      `;
    
    case 'checkbox':
      return `
        <label class="checkbox-label">
          <input type="checkbox" disabled />
          ${field.fieldLabel} ${requiredMark}
        </label>
        ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
      `;
    
    case 'checkboxGroup':
      return `
        <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
        <div class="checkbox-group">
          ${field.options.map(opt => `
            <label class="checkbox-label">
              <input type="checkbox" disabled />
              ${opt}
            </label>
          `).join('')}
        </div>
        ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
      `;
    
    case 'date':
      return `
        <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
        <input type="date" class="form-input" disabled />
        ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
      `;
    
    case 'search':
      return `
        <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
        <div class="search-field">
          <span class="search-icon">🔍</span>
          <input type="text" class="form-input" placeholder="${field.placeholder}" disabled />
        </div>
        ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
      `;
    
    case 'file':
      return `
        <label class="form-label">${field.fieldLabel} ${requiredMark}</label>
        <div class="file-upload">
          <button class="btn btn-secondary btn-sm" disabled>📎 Choose File</button>
          <span class="help-text">Accepted: ${field.acceptedTypes.join(', ')} | Max: ${field.maxSize}MB</span>
        </div>
        ${field.helpText ? `<span class="help-text">${field.helpText}</span>` : ''}
      `;
    
    default:
      return `<span class="text-muted">Field type: ${field.fieldType}</span>`;
  }
}

// 7. Edit field modal
window.editField = function(blockId, sectionId, fieldId) {
  const block = templateConfig.blocks.find(b => b.blockId === blockId);
  const section = block?.sections.find(s => s.sectionId === sectionId);
  const field = section?.fields.find(f => f.fieldId === fieldId);
  
  if (!field) return;
  
  const modal = document.createElement('div');
  modal.className = 'modal-overlay';
  modal.id = 'field-edit-modal';
  modal.innerHTML = `
    <div class="modal-content modal-large">
      <div class="modal-header">
        <h3>Configure Field</h3>
        <button class="btn-icon" onclick="closeModal()">✖</button>
      </div>
      
      <div class="modal-body">
        <div class="form-grid">
          <!-- Basic Configuration -->
          <div class="config-group">
            <h4>Basic Configuration</h4>
            
            <div class="form-group">
              <label class="form-label">Field Label</label>
              <input type="text" class="form-input" id="edit-label" value="${field.fieldLabel}" />
            </div>
            
            <div class="form-group">
              <label class="form-label">Field Type</label>
              <select class="form-select" id="edit-type" onchange="updateFieldTypeOptions(this.value)">
                ${Object.entries(fieldTypes).map(([key, val]) => 
                  `<option value="${key}" ${field.fieldType === key ? 'selected' : ''}>${val.icon} ${val.label}</option>`
                ).join('')}
              </select>
            </div>
            
            <div class="form-group">
              <label class="checkbox-label">
                <input type="checkbox" id="edit-required" ${field.required ? 'checked' : ''} />
                Required Field
              </label>
            </div>
            
            <div class="form-group">
              <label class="checkbox-label">
                <input type="checkbox" id="edit-enabled" ${field.enabled ? 'checked' : ''} />
                Field Enabled
              </label>
            </div>
          </div>
          
          <!-- Field-Specific Configuration -->
          <div class="config-group">
            <h4>Field-Specific Settings</h4>
            
            <div id="field-specific-config">
              ${renderFieldSpecificConfig(field)}
            </div>
          </div>
          
          <!-- Conditional Visibility -->
          <div class="config-group full-width">
            <h4>Conditional Visibility</h4>
            <div id="conditional-config">
              ${field.conditionalVisibility ? renderConditionalRulesSummary(field.conditionalVisibility) : '<p class="text-muted">No conditional rules set</p>'}
            </div>
            <button class="btn btn-secondary btn-sm" onclick="openConditionalRulesBuilder('${blockId}', '${sectionId}', '${fieldId}')">
              ${field.conditionalVisibility ? '✏️ Edit Rules' : '➕ Add Rules'}
            </button>
          </div>
        </div>
      </div>
      
      <div class="modal-footer">
        <button class="btn btn-secondary" onclick="closeModal()">Cancel</button>
        <button class="btn btn-primary" onclick="saveFieldConfig('${blockId}', '${sectionId}', '${fieldId}')">Save Changes</button>
      </div>
    </div>
  `;
  
  document.body.appendChild(modal);
};

// 8. Render field-specific configuration options
function renderFieldSpecificConfig(field) {
  let html = '';
  
  // Placeholder
  if (['text', 'textarea', 'search'].includes(field.fieldType)) {
    html += `
      <div class="form-group">
        <label class="form-label">Placeholder Text</label>
        <input type="text" class="form-input" id="edit-placeholder" value="${field.placeholder}" />
      </div>
    `;
  }
  
  // Help Text
  html += `
    <div class="form-group">
      <label class="form-label">Help Text</label>
      <textarea class="form-textarea" id="edit-helptext" rows="2">${field.helpText}</textarea>
    </div>
  `;
  
  // Default Value
  if (!field.readonly) {
    html += `
      <div class="form-group">
        <label class="form-label">Default Value</label>
        <input type="text" class="form-input" id="edit-default" value="${field.defaultValue}" />
      </div>
    `;
  }
  
  // Options for dropdown/checkbox group
  if (['dropdown', 'checkboxGroup', 'radio'].includes(field.fieldType)) {
    html += `
      <div class="form-group">
        <label class="form-label">Options (one per line)</label>
        <textarea class="form-textarea" id="edit-options" rows="5">${field.options.join('\n')}</textarea>
      </div>
    `;
    
    if (field.fieldType === 'dropdown') {
      html += `
        <div class="form-group">
          <label class="checkbox-label">
            <input type="checkbox" id="edit-multiple" ${field.allowMultiple ? 'checked' : ''} />
            Allow Multiple Selection
          </label>
        </div>
      `;
    }
  }
  
  // Data Source for search fields
  if (field.fieldType === 'search') {
    html += `
      <div class="form-group">
        <label class="form-label">Data Source</label>
        <select class="form-select" id="edit-datasource">
          <option value="oneUNOPS" ${field.dataSource === 'oneUNOPS' ? 'selected' : ''}>oneUNOPS</option>
          <option value="Event ID" ${field.dataSource === 'Event ID' ? 'selected' : ''}>Event ID</option>
          <option value="RDAT" ${field.dataSource === 'RDAT' ? 'selected' : ''}>oneUNOPS RDAT</option>
        </select>
      </div>
    `;
  }
  
  // File upload specific
  if (field.fieldType === 'file') {
    html += `
      <div class="form-group">
        <label class="form-label">Accepted File Types</label>
        <input type="text" class="form-input" id="edit-acceptedtypes" value="${field.acceptedTypes.join(', ')}" 
               placeholder=".pdf, .doc, .docx" />
      </div>
      <div class="form-group">
        <label class="form-label">Max File Size (MB)</label>
        <input type="number" class="form-input" id="edit-maxsize" value="${field.maxSize || 10}" />
      </div>
    `;
  }
  
  // Textarea rows
  if (field.fieldType === 'textarea') {
    html += `
      <div class="form-group">
        <label class="form-label">Number of Rows</label>
        <input type="number" class="form-input" id="edit-rows" value="${field.rows || 3}" min="2" max="20" />
      </div>
    `;
  }
  
  // Validation
  if (['text', 'textarea'].includes(field.fieldType)) {
    html += `
      <div class="form-group">
        <label class="form-label">Validation</label>
        <div class="validation-inputs">
          <input type="number" class="form-input" id="edit-minlength" placeholder="Min length" 
                 value="${field.validation?.minLength || ''}" />
          <input type="number" class="form-input" id="edit-maxlength" placeholder="Max length" 
                 value="${field.validation?.maxLength || ''}" />
        </div>
      </div>
    `;
  }
  
  return html;
}

// 9. Save field configuration
window.saveFieldConfig = function(blockId, sectionId, fieldId) {
  const block = templateConfig.blocks.find(b => b.blockId === blockId);
  const section = block?.sections.find(s => s.sectionId === sectionId);
  const field = section?.fields.find(f => f.fieldId === fieldId);
  
  if (!field) return;
  
  // Update field properties from form
  field.fieldLabel = document.getElementById('edit-label')?.value || field.fieldLabel;
  field.fieldType = document.getElementById('edit-type')?.value || field.fieldType;
  field.required = document.getElementById('edit-required')?.checked || false;
  field.enabled = document.getElementById('edit-enabled')?.checked || false;
  field.placeholder = document.getElementById('edit-placeholder')?.value || '';
  field.helpText = document.getElementById('edit-helptext')?.value || '';
  field.defaultValue = document.getElementById('edit-default')?.value || '';
  
  // Type-specific properties
  if (['dropdown', 'checkboxGroup', 'radio'].includes(field.fieldType)) {
    const optionsText = document.getElementById('edit-options')?.value || '';
    field.options = optionsText.split('\n').filter(o => o.trim() !== '');
    field.allowMultiple = document.getElementById('edit-multiple')?.checked || false;
  }
  
  if (field.fieldType === 'search') {
    field.dataSource = document.getElementById('edit-datasource')?.value || 'oneUNOPS';
  }
  
  if (field.fieldType === 'file') {
    const acceptedTypesText = document.getElementById('edit-acceptedtypes')?.value || '';
    field.acceptedTypes = acceptedTypesText.split(',').map(t => t.trim()).filter(t => t !== '');
    field.maxSize = parseInt(document.getElementById('edit-maxsize')?.value) || 10;
  }
  
  if (field.fieldType === 'textarea') {
    field.rows = parseInt(document.getElementById('edit-rows')?.value) || 3;
  }
  
  // Validation
  if (['text', 'textarea'].includes(field.fieldType)) {
    field.validation = field.validation || {};
    const minLength = document.getElementById('edit-minlength')?.value;
    const maxLength = document.getElementById('edit-maxlength')?.value;
    if (minLength) field.validation.minLength = parseInt(minLength);
    if (maxLength) field.validation.maxLength = parseInt(maxLength);
  }
  
  // Update template config modification date
  templateConfig.modifiedDate = new Date().toISOString();
  
  // Re-render the block config view
  updateCanvasView();
  
  // Close modal
  closeModal();
  
  // Show success message
  showNotification('Field configuration saved successfully', 'success');
};

// 10. Helper functions
window.closeModal = function() {
  const modal = document.querySelector('.modal-overlay');
  if (modal) modal.remove();
};

function showNotification(message, type = 'info') {
  const notification = document.createElement('div');
  notification.className = `notification notification-${type}`;
  notification.textContent = message;
  notification.style.cssText = `
    position: fixed;
    top: 20px;
    right: 20px;
    padding: 12px 24px;
    background: ${type === 'success' ? 'var(--success-500)' : 'var(--info-400)'};
    color: white;
    border-radius: var(--radius-md);
    box-shadow: var(--shadow-card);
    z-index: 10000;
  `;
  document.body.appendChild(notification);
  setTimeout(() => notification.remove(), 3000);
}

function renderConditionalRulesSummary(rules) {
  if (!rules || !rules.rules || rules.rules.length === 0) {
    return '<p class="text-muted">No rules defined</p>';
  }
  
  return `
    <div class="conditional-rules-summary">
      <div class="rules-logic-badge">${rules.logic}</div>
      <ul class="rules-list">
        ${rules.rules.map(rule => `
          <li class="rule-item">
            <strong>${rule.field}</strong> ${rule.operator} ${rule.value !== null ? `"${rule.value}"` : ''}
          </li>
        `).join('')}
      </ul>
    </div>
  `;
}
```

---

## Integration Steps

### Step 1: Add Data Structure

Copy the data structure code (Section 1) to your `<script>` tag.

### Step 2: Update Block Drop Handler

Modify your existing `addSectionToCanvas` function:

```javascript
function addSectionToCanvas(blockType) {
  let blockConfig;
  
  switch (blockType) {
    case 'general-info':
      blockConfig = initializeGeneralInfoBlock();
      break;
    case 'project':
      blockConfig = initializeProjectBlock();
      break;
    // ... other blocks
    default:
      console.error('Unknown block type:', blockType);
      return;
  }
  
  templateConfig.blocks.push(blockConfig);
  updateCanvasView();
  switchToBlock(templateConfig.blocks.length - 1);
}
```

### Step 3: Update Canvas View Rendering

```javascript
function updateCanvasView() {
  const canvas = document.getElementById('canvas');
  
  if (templateConfig.blocks.length === 0) {
    canvas.innerHTML = `
      <div class="canvas-empty">
        <div class="canvas-empty-icon">🎨</div>
        <h2>Start Building Your Template</h2>
        <p>Drag and drop blocks from the left sidebar to begin</p>
      </div>
    `;
    return;
  }
  
  // Get the active block
  const activeBlock = templateConfig.blocks[activeBlockIndex];
  
  if (!activeBlock) return;
  
  // Render the block configuration view
  canvas.innerHTML = renderBlockConfigView(activeBlock);
}
```

### Step 4: Add Supporting Functions

```javascript
window.updateBlockName = function(blockId, newName) {
  const block = templateConfig.blocks.find(b => b.blockId === blockId);
  if (block) {
    block.blockName = newName;
    templateConfig.modifiedDate = new Date().toISOString();
  }
};

window.toggleBlockEnabled = function(blockId, enabled) {
  const block = templateConfig.blocks.find(b => b.blockId === blockId);
  if (block) {
    block.enabled = enabled;
    templateConfig.modifiedDate = new Date().toISOString();
    updateCanvasView();
  }
};

window.deleteBlock = function(blockId) {
  if (!confirm('Are you sure you want to delete this block?')) return;
  
  const index = templateConfig.blocks.findIndex(b => b.blockId === blockId);
  if (index > -1) {
    templateConfig.blocks.splice(index, 1);
    if (activeBlockIndex >= templateConfig.blocks.length) {
      activeBlockIndex = Math.max(0, templateConfig.blocks.length - 1);
    }
    templateConfig.modifiedDate = new Date().toISOString();
    updateBlockTabs();
    updateCanvasView();
  }
};

window.deleteField = function(blockId, sectionId, fieldId) {
  if (!confirm('Are you sure you want to delete this field?')) return;
  
  const block = templateConfig.blocks.find(b => b.blockId === blockId);
  const section = block?.sections.find(s => s.sectionId === sectionId);
  
  if (section) {
    const index = section.fields.findIndex(f => f.fieldId === fieldId);
    if (index > -1) {
      section.fields.splice(index, 1);
      templateConfig.modifiedDate = new Date().toISOString();
      updateCanvasView();
    }
  }
};
```

---

## CSS Additions

Add these styles to support the new UI:

```css
/* Field Configuration Cards */
.field-config-card {
  background: var(--surface-0);
  border: 1px solid var(--surface-200);
  border-radius: var(--radius-md);
  padding: var(--spacing-md);
  margin-bottom: var(--spacing-sm);
  transition: all 0.2s;
}

.field-config-card:hover {
  border-color: var(--primary-400);
  box-shadow: 0 2px 8px rgba(0, 102, 154, 0.1);
}

.field-config-card.disabled {
  opacity: 0.5;
}

.field-config-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--spacing-sm);
}

.field-info {
  display: flex;
  align-items: center;
  gap: var(--spacing-sm);
}

.field-type-badge {
  font-size: 18px;
}

.field-label {
  font-weight: 600;
  color: var(--text-primary);
}

.badge {
  display: inline-block;
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 11px;
  font-weight: 600;
  text-transform: uppercase;
}

.badge-required {
  background: var(--danger-50);
  color: var(--danger-500);
}

.badge-conditional {
  background: var(--info-50);
  color: var(--info-400);
}

.field-config-card-body {
  padding-top: var(--spacing-sm);
  border-top: 1px solid var(--surface-100);
}

.field-config-preview {
  pointer-events: none;
  opacity: 0.8;
}

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.modal-content {
  background: var(--surface-0);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-modal);
  max-width: 600px;
  width: 90%;
  max-height: 90vh;
  display: flex;
  flex-direction: column;
}

.modal-large {
  max-width: 900px;
}

.modal-header {
  padding: var(--spacing-lg);
  border-bottom: 1px solid var(--surface-200);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.modal-body {
  padding: var(--spacing-lg);
  overflow-y: auto;
  flex: 1;
}

.modal-footer {
  padding: var(--spacing-lg);
  border-top: 1px solid var(--surface-200);
  display: flex;
  justify-content: flex-end;
  gap: var(--spacing-sm);
}

/* Config Groups */
.config-group {
  background: var(--surface-50);
  border-radius: var(--radius-md);
  padding: var(--spacing-md);
  margin-bottom: var(--spacing-md);
}

.config-group h4 {
  margin-bottom: var(--spacing-md);
  color: var(--text-primary);
  font-size: 16px;
}

.config-group.full-width {
  grid-column: 1 / -1;
}

/* Form Grid */
.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--spacing-lg);
}

/* Validation Inputs */
.validation-inputs {
  display: flex;
  gap: var(--spacing-sm);
}

.validation-inputs .form-input {
  flex: 1;
}

/* Notification */
.notification {
  animation: slideIn 0.3s ease-out;
}

@keyframes slideIn {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}
```

---

## Next Steps

1. **Test the General Information Block**: Drop it on canvas and verify all fields render correctly
2. **Implement Field Editing**: Click edit on any field and test the configuration modal
3. **Add Conditional Rules Builder**: Implement the full rules builder UI
4. **Replicate for Other Blocks**: Use the same pattern for Project Details, Requisition & Lots, etc.
5. **Add Preview Mode**: Implement the template preview functionality
6. **Add Export/Import**: Allow saving and loading templates as JSON

---

## Key Takeaways

✅ Every field from `tender-create.html` becomes configurable in `template-builder-workspace.html`
✅ Admin configures default values, validation, and conditional visibility
✅ The same data structure can be used for both configuration and rendering
✅ Reusable components make implementation scalable
✅ Preview mode lets admins see exactly what tender authors will see

This pattern is repeatable for all 7 blocks! 🎉

