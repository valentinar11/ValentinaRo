# Jira Stories: MVP Admin Configuration Tools Sprint

**Sprint Goal:** Enable Business Admins to configure Fields Library, Criteria Library, and build Tender Document Templates so they can start creating templates immediately.

**Scope:** Admin configuration interfaces only (not tender authoring for end users)

**Approach:** Seed known Blocks directly in database for MVP. Block creation UI comes in later sprint.

---

## EPIC: Admin Configuration Foundation

**Epic Description:** Build the foundational configuration tools that enable Business Admins to set up Fields Library, Criteria Library, and assemble Tender Document Templates from pre-seeded Blocks.

**Business Value:** Unblock business admins to begin template configuration and testing immediately. Establish data model foundation for all future features.

**Out of Scope (Later Sprints):**
- Block creation UI (blocks seeded in DB for MVP)
- Form Builder UI
- Tender authoring interface for Tender Authors
- CLM content management
- Policy & Rules Engine configuration UI

---

## STORY 1: Data Model - Complete eTendering Foundation

**As a** System Architect  
**I want to** implement comprehensive data models for Fields, Blocks, Templates, and Criteria  
**So that** all configuration tools have solid database foundation

### Acceptance Criteria

**AC1: Fields Library Tables**

```sql
-- Tier 1: Core Fields by Entity
CREATE TABLE entities (
    entity_id UUID PRIMARY KEY,
    entity_name VARCHAR(100) NOT NULL,  -- e.g., 'Tender_Entity', 'Vendor_Entity'
    entity_type ENUM('CORE', 'CUSTOM') NOT NULL,
    description TEXT,
    created_at TIMESTAMP DEFAULT NOW(),
    created_by UUID,
    status ENUM('ACTIVE', 'DEPRECATED') DEFAULT 'ACTIVE'
);

CREATE TABLE fields (
    field_id UUID PRIMARY KEY,
    entity_id UUID REFERENCES entities(entity_id),  -- NULL for unstructured fields
    field_name VARCHAR(100) NOT NULL,  -- e.g., 'TenderTitle', 'BidSecurityAmount'
    display_name VARCHAR(200) NOT NULL,  -- Human-readable
    data_type ENUM('TEXT', 'NUMERIC', 'DATE', 'LOV', 'BOOLEAN', 'TABLE', 'RICH_TEXT') NOT NULL,
    field_tier ENUM('CORE_STANDARD', 'ENTITY_CUSTOM', 'UNSTRUCTURED') NOT NULL,
    validation_rules JSONB,  -- {mandatory: true, min: 0, max: 999999, character_limit: 500}
    lov_name VARCHAR(100),  -- Reference to list of values if data_type = LOV
    default_value TEXT,
    read_only_for VARCHAR[],  -- Array of role IDs
    placeholder_text TEXT,
    help_text TEXT,
    created_at TIMESTAMP DEFAULT NOW(),
    created_by UUID,
    status ENUM('ACTIVE', 'DEPRECATED') DEFAULT 'ACTIVE',
    UNIQUE(entity_id, field_name)
);

-- List of Values definitions
CREATE TABLE list_of_values (
    lov_id UUID PRIMARY KEY,
    lov_name VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    values JSONB NOT NULL,  -- [{value: 'RFP', label: 'Request for Proposal', order: 1}]
    is_system_defined BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW()
);
```

**AC2: Criteria Library Tables**

```sql
CREATE TABLE criteria_definitions (
    criterion_id UUID PRIMARY KEY,
    criterion_code VARCHAR(100) UNIQUE NOT NULL,  -- e.g., 'CRIT_PROCUREMENT_METHOD'
    criterion_name VARCHAR(200) NOT NULL,
    source_field_id UUID REFERENCES fields(field_id) NOT NULL,
    associated_logic ENUM(
        'BOOLEAN_YES_NO_CHECK', 
        'NUMERIC_THRESHOLD', 
        'LOV_SELECTION',
        'DATE_COMPARISON',
        'TEXT_MATCH'
    ) NOT NULL,
    logic_parameters JSONB,  -- {operator: '>', threshold: 100000}
    description TEXT,
    created_at TIMESTAMP DEFAULT NOW(),
    created_by UUID,
    status ENUM('ACTIVE', 'INACTIVE') DEFAULT 'ACTIVE'
);
```

**AC3: Blocks Library Tables**

```sql
CREATE TABLE blocks (
    block_id UUID PRIMARY KEY,
    block_code VARCHAR(100) UNIQUE NOT NULL,  -- e.g., 'GEN_INFO_001'
    block_name VARCHAR(200) NOT NULL,  -- e.g., 'General Information'
    block_type ENUM('PROVIDED_BY_PROCUREMENT', 'COLLECTED_FROM_VENDORS', 'HYBRID') NOT NULL,
    visibility ENUM('PUBLIC', 'INTERNAL') NOT NULL,
    description TEXT,
    layout_config JSONB,  -- UI layout preferences
    is_reusable BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT NOW(),
    created_by UUID,
    status ENUM('ACTIVE', 'DEPRECATED') DEFAULT 'ACTIVE',
    
    -- Validation: COLLECTED and HYBRID must be PUBLIC
    CONSTRAINT chk_visibility CHECK (
        (block_type = 'PROVIDED_BY_PROCUREMENT') OR 
        (visibility = 'PUBLIC')
    )
);

CREATE TABLE block_fields (
    block_field_id UUID PRIMARY KEY,
    block_id UUID REFERENCES blocks(block_id) ON DELETE CASCADE,
    field_id UUID REFERENCES fields(field_id),
    form_id UUID REFERENCES forms(form_id),  -- If incorporating a form instead of field
    display_order INTEGER NOT NULL,
    is_required_in_block BOOLEAN DEFAULT FALSE,
    conditional_logic JSONB,  -- {if_field_id: UUID, if_value: 'YES', then: 'SHOW'}
    section_name VARCHAR(100),  -- Grouping within block
    created_at TIMESTAMP DEFAULT NOW(),
    
    -- Either field_id or form_id must be populated, not both
    CONSTRAINT chk_field_or_form CHECK (
        (field_id IS NOT NULL AND form_id IS NULL) OR
        (field_id IS NULL AND form_id IS NOT NULL)
    )
);

-- For nested blocks
CREATE TABLE block_nested_blocks (
    parent_block_id UUID REFERENCES blocks(block_id) ON DELETE CASCADE,
    child_block_id UUID REFERENCES blocks(block_id) ON DELETE CASCADE,
    display_order INTEGER NOT NULL,
    PRIMARY KEY (parent_block_id, child_block_id)
);
```

**AC4: Templates Library Tables**

```sql
CREATE TABLE tender_document_templates (
    template_id UUID PRIMARY KEY,
    template_code VARCHAR(100) UNIQUE NOT NULL,  -- e.g., 'RFP_GOODS_001'
    template_name VARCHAR(200) NOT NULL,  -- e.g., 'RFP for Goods'
    solicitation_type VARCHAR(50),  -- For filtering: RFP, ITB, RFQ, EOI
    procurement_type VARCHAR(50),  -- Goods, Services, Works
    granular_category VARCHAR(100),  -- IT Equipment, Consulting, Construction
    description TEXT,
    created_at TIMESTAMP DEFAULT NOW(),
    created_by UUID,
    last_modified_at TIMESTAMP,
    status ENUM('DRAFT', 'ACTIVE', 'DEPRECATED') DEFAULT 'DRAFT'
);

CREATE TABLE template_blocks (
    template_block_id UUID PRIMARY KEY,
    template_id UUID REFERENCES tender_document_templates(template_id) ON DELETE CASCADE,
    block_id UUID REFERENCES blocks(block_id),
    display_order INTEGER NOT NULL,
    is_mandatory_in_template BOOLEAN DEFAULT FALSE,  -- In MVP, admin sets this
    section_group VARCHAR(100),  -- Grouping: 'General', 'Technical', 'Evaluation'
    created_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(template_id, block_id),
    UNIQUE(template_id, display_order)
);
```

**AC5: Forms Library Tables** (For completeness, even though UI comes later)

```sql
CREATE TABLE forms (
    form_id UUID PRIMARY KEY,
    form_code VARCHAR(100) UNIQUE NOT NULL,
    form_name VARCHAR(200) NOT NULL,
    form_purpose VARCHAR(500),  -- e.g., 'Bank Guarantee Form'
    created_at TIMESTAMP DEFAULT NOW(),
    created_by UUID,
    status ENUM('ACTIVE', 'DEPRECATED') DEFAULT 'ACTIVE'
);

CREATE TABLE form_fields (
    form_field_id UUID PRIMARY KEY,
    form_id UUID REFERENCES forms(form_id) ON DELETE CASCADE,
    field_id UUID REFERENCES fields(field_id),  -- Links to unstructured fields
    display_order INTEGER NOT NULL,
    is_required_in_form BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW()
);
```

**AC6: Supporting Tables**

```sql
-- Attachments (for blocks and templates)
CREATE TABLE attachments (
    attachment_id UUID PRIMARY KEY,
    attached_to_type ENUM('BLOCK', 'TEMPLATE', 'TENDER_INSTANCE'),
    attached_to_id UUID NOT NULL,
    file_name VARCHAR(255) NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    file_size BIGINT,
    file_type VARCHAR(100),
    visibility ENUM('PUBLIC', 'INTERNAL') DEFAULT 'PUBLIC',
    uploaded_by UUID,
    uploaded_at TIMESTAMP DEFAULT NOW(),
    
    -- Validation: If attached to PUBLIC block, can be PUBLIC or INTERNAL
    -- If attached to INTERNAL block, must be INTERNAL
    -- This validation handled at application layer
);

-- Audit trail
CREATE TABLE audit_log (
    log_id UUID PRIMARY KEY,
    entity_type VARCHAR(50) NOT NULL,  -- 'FIELD', 'BLOCK', 'TEMPLATE', etc.
    entity_id UUID NOT NULL,
    action VARCHAR(50) NOT NULL,  -- 'CREATED', 'UPDATED', 'DELETED', 'STATUS_CHANGED'
    changed_by UUID NOT NULL,
    changed_at TIMESTAMP DEFAULT NOW(),
    changes JSONB,  -- {old_value: {}, new_value: {}}
    ip_address INET
);
```

**AC7: Seed Data Script for MVP Blocks**

Create SQL script to seed known blocks for MVP:

```sql
-- Seed Core Entities
INSERT INTO entities (entity_id, entity_name, entity_type, description) VALUES
('e1', 'Tender_Entity', 'CORE', 'Core fields related to tender details'),
('e2', 'Vendor_Entity', 'CORE', 'Core fields related to vendor information'),
('e3', 'Pricing_Entity', 'CORE', 'Core fields related to pricing and financial'),
('e4', 'Evaluation_Entity', 'CORE', 'Core fields for evaluation and scoring'),
('e5', 'Technical_Specification_Entity', 'CORE', 'Technical requirement fields'),
('e6', 'Legal_and_Compliance_Entity', 'CORE', 'Legal and compliance fields');

-- Seed Core Fields (examples - complete list needed)
INSERT INTO fields (field_id, entity_id, field_name, display_name, data_type, field_tier, validation_rules) VALUES
('f1', 'e1', 'TenderTitle', 'Tender Title', 'TEXT', 'CORE_STANDARD', '{"mandatory": true, "character_limit": 200}'),
('f2', 'e1', 'ProcurementMethod', 'Procurement Method', 'LOV', 'CORE_STANDARD', '{"mandatory": true}'),
('f3', 'e1', 'EstimatedOverallValue', 'Estimated Value', 'NUMERIC', 'CORE_STANDARD', '{"mandatory": true, "min": 0}'),
('f4', 'e1', 'SubmissionDeadline', 'Submission Deadline', 'DATE', 'CORE_STANDARD', '{"mandatory": true}'),
('f5', 'e2', 'VendorName', 'Vendor Name', 'TEXT', 'CORE_STANDARD', '{"mandatory": true}'),
('f6', 'e3', 'UnitPrice', 'Unit Price', 'NUMERIC', 'CORE_STANDARD', '{"mandatory": true, "min": 0}'),
('f7', 'e4', 'TechnicalScore', 'Technical Score', 'NUMERIC', 'CORE_STANDARD', '{"min": 0, "max": 100}');

-- Seed LOVs
INSERT INTO list_of_values (lov_id, lov_name, values, is_system_defined) VALUES
('lov1', 'ProcurementMethods', '[
    {"value": "RFP", "label": "Request for Proposal", "order": 1},
    {"value": "ITB", "label": "Invitation to Bid", "order": 2},
    {"value": "RFQ", "label": "Request for Quotation", "order": 3},
    {"value": "EOI", "label": "Expression of Interest", "order": 4}
]', TRUE);

-- Seed Known Blocks for MVP
INSERT INTO blocks (block_id, block_code, block_name, block_type, visibility, description) VALUES
('b1', 'GEN_INFO_001', 'General Information', 'PROVIDED_BY_PROCUREMENT', 'PUBLIC', 'Basic tender details and reference information'),
('b2', 'TECH_SPEC_001', 'Technical Specifications', 'PROVIDED_BY_PROCUREMENT', 'PUBLIC', 'Technical requirements and specifications'),
('b3', 'EVAL_CRITERIA_001', 'Evaluation Criteria', 'HYBRID', 'PUBLIC', 'Evaluation criteria with vendor response section'),
('b4', 'PRICING_001', 'Pricing Schedule', 'COLLECTED_FROM_VENDORS', 'PUBLIC', 'Vendor pricing and financial proposal'),
('b5', 'BIDDER_INFO_001', 'Bidder Information Form', 'COLLECTED_FROM_VENDORS', 'PUBLIC', 'Vendor registration and company details'),
('b6', 'EVAL_WORKSHEET_001', 'Evaluation Worksheet', 'PROVIDED_BY_PROCUREMENT', 'INTERNAL', 'Internal evaluation scoring sheet'),
('b7', 'FIN_GUARANTEE_001', 'Financial Guarantees', 'PROVIDED_BY_PROCUREMENT', 'PUBLIC', 'Bid security and bank guarantee requirements'),
('b8', 'SCOPE_WORK_001', 'Scope of Work', 'PROVIDED_BY_PROCUREMENT', 'PUBLIC', 'Detailed scope and deliverables'),
('b9', 'SUBMISSIONS_REQ_001', 'Submission Requirements', 'PROVIDED_BY_PROCUREMENT', 'PUBLIC', 'Instructions for bid submission'),
('b10', 'CONTRACT_TERMS_001', 'Contract Terms', 'PROVIDED_BY_PROCUREMENT', 'PUBLIC', 'General conditions of contract'),
('b11', 'BUDGET_ANALYSIS_001', 'Budget Analysis', 'PROVIDED_BY_PROCUREMENT', 'INTERNAL', 'Internal budget breakdown and analysis'),
('b12', 'BLOCK_CATALOGUE_ITEM', 'Product Catalogue Item', 'PROVIDED_BY_PROCUREMENT', 'PUBLIC', 'UN WEB BUY catalogue integration block');

-- Seed Block Fields relationships (examples - link fields to blocks)
INSERT INTO block_fields (block_field_id, block_id, field_id, display_order, is_required_in_block, section_name) VALUES
('bf1', 'b1', 'f1', 1, TRUE, 'Basic Information'),  -- General Info: TenderTitle
('bf2', 'b1', 'f2', 2, TRUE, 'Basic Information'),  -- General Info: ProcurementMethod
('bf3', 'b1', 'f3', 3, TRUE, 'Financial'),  -- General Info: EstimatedValue
('bf4', 'b1', 'f4', 4, TRUE, 'Timeline'),  -- General Info: SubmissionDeadline
('bf5', 'b5', 'f5', 1, TRUE, 'Company Details'),  -- Bidder Info: VendorName
('bf6', 'b4', 'f6', 1, TRUE, 'Pricing');  -- Pricing Schedule: UnitPrice
```

**AC2: Criteria Library Tables** (Already defined above)

**AC3: Templates Library Tables** (Already defined above)

**AC4: Database Indexes for Performance**

```sql
-- Performance indexes
CREATE INDEX idx_fields_entity ON fields(entity_id) WHERE status = 'ACTIVE';
CREATE INDEX idx_fields_tier ON fields(field_tier);
CREATE INDEX idx_blocks_type ON blocks(block_type);
CREATE INDEX idx_blocks_visibility ON blocks(visibility);
CREATE INDEX idx_blocks_status ON blocks(status) WHERE status = 'ACTIVE';
CREATE INDEX idx_template_blocks_template ON template_blocks(template_id);
CREATE INDEX idx_template_blocks_order ON template_blocks(template_id, display_order);
CREATE INDEX idx_criteria_source_field ON criteria_definitions(source_field_id);
```

**AC5: Data Validation at Database Level**

- Foreign key constraints properly defined
- Check constraints for enums
- Unique constraints where appropriate
- NOT NULL constraints on required fields
- Cascading deletes configured appropriately

**AC6: Migration Scripts**

- Up migration creates all tables
- Down migration drops all tables (with cascade)
- Seed data script separate (can be run independently)
- Version controlled

### Technical Notes

**Database:** PostgreSQL 14+ (for JSONB support and advanced features)

**Naming Convention:** snake_case for tables/columns, UPPER_CASE for enums

**JSONB Fields:** Use for flexible configuration (validation_rules, logic_parameters, layout_config) to avoid frequent schema changes

**Audit:** All configuration tables track created_by, created_at, and have status fields

**Story Points:** 8

---

## STORY 2: Fields Library UI - Entity & Field Management

**As a** Business Admin  
**I want to** view and manage entities and fields in the Fields Library  
**So that** I can configure the foundational data structure for the system

### Acceptance Criteria

**AC1: Fields Library Page Layout**

Three-tab interface matching screenshot:
- **Core Fields** tab (default active)
- **Custom Entities** tab
- **Custom Fields** tab (unstructured)

Header includes:
- Title: "Fields Library"
- Subtitle: "Manage all field definitions across the system"
- Action button: "+ Add Field" (enabled based on current tab)

**AC2: Core Fields Tab - Entity List Display**

Display all entities from `entities` table where `entity_type = 'CORE'` and `status = 'ACTIVE'`:

Each entity shown as expandable card:
```
┌─────────────────────────────────────────────────────┐
│ 📋 Tender_Entity                          [Edit]    │
├─────────────────────────────────────────────────────┤
│ Field Name       | Data Type | Validation | Status  │
│ TenderTitle      | TEXT      | Mandatory  | Active  │
│ ProcurementMethod| LOV       | Mandatory  | Active  │
│ EstimatedValue   | NUMERIC   | Min: 0     | Active  │
│ SubmissionDeadline| DATE     | Mandatory  | Active  │
└─────────────────────────────────────────────────────┘
```

Cards display:
- Entity name with icon (📋 Tender, 👤 Vendor, 💰 Pricing, 📊 Evaluation, 🔧 Technical, ⚖️ Legal)
- "Edit" button (opens entity editor - Story 3)
- Table of fields in entity with columns: Field Name, Data Type, Validation Rules, Status
- Action button per field: "⚙️ Edit" (opens field editor - Story 4)

**AC3: Core Fields Tab - Fields Display**

For each field in table, show:
- **Field Name:** Bold, left-aligned (e.g., "TenderTitle")
- **Data Type:** Badge style (TEXT, NUMERIC, DATE, LOV, BOOLEAN)
- **Validation:** Abbreviated display ("Mandatory" or "Min: 0, Max: 999999" or "Char Limit: 500")
- **Status:** Green "Active" badge or Gray "Deprecated" badge
- **Actions:** Gear icon button "⚙️ Edit"

**AC4: Custom Entities Tab**

Display all entities where `entity_type = 'CUSTOM'`:

If none exist:
```
┌─────────────────────────────────────────────────────┐
│ ℹ️ No Custom Entities Defined                       │
│                                                      │
│ Create custom entities to organize fields for       │
│ unique business needs                               │
│                                                      │
│ [+ Create Custom Entity]                            │
└─────────────────────────────────────────────────────┘
```

If custom entities exist, display in same card format as Core Fields tab

**AC5: Custom Fields Tab (Unstructured)**

Display all fields where `entity_id IS NULL` (Tier 3: Unstructured):

Table view with columns:
- Field Name
- Data Type  
- Validation Rules
- Created Date
- Status
- Actions (Edit, Delete if not in use)

Alert message at top:
```
⚠️ Unstructured custom fields for unique, one-off requirements
These fields are not grouped by entity and available for ad-hoc use in Forms
```

Action button: "+ Add Custom Field"

**AC6: Responsive Design**

- Minimum viewport: 1280px width
- Entity cards stack vertically
- Field tables horizontally scrollable if needed
- Fixed header on scroll

**AC7: Search & Filter**

Global search bar searches across:
- Field names
- Entity names
- Descriptions

Filter dropdown:
- All Statuses
- Active Only
- Deprecated Only

Filter by Data Type:
- All Types
- TEXT
- NUMERIC
- DATE
- LOV
- BOOLEAN

**AC8: Empty States**

If no fields exist in entity:
```
No fields defined for this entity yet
[+ Add Field to Entity]
```

**AC9: Loading States**

- Skeleton loaders while fetching data
- Spinner for actions (Edit, Delete)
- Success/error notifications (toast messages)

**AC10: Permissions**

Only users with `ADMIN` or `SYSTEM_CONFIGURATOR` role can access this page

### Technical Notes

**API Endpoints:**
```
GET /api/admin/entities
GET /api/admin/entities/{entity_id}/fields
GET /api/admin/fields (all fields)
GET /api/admin/fields?entity_id=null (unstructured only)
GET /api/admin/fields?search=tender
```

**Frontend Framework:** React or Vue.js with component library (Material-UI, Ant Design, or Chakra UI)

**State Management:** Redux or Context API for managing field/entity data

**Story Points:** 8

---

## STORY 3: Fields Library UI - Create/Edit Entity

**As a** Business Admin  
**I want to** create new custom entities and edit existing entities  
**So that** I can organize fields into logical business groupings

### Acceptance Criteria

**AC1: Create Custom Entity - Modal/Page**

Clicking "+ Create Custom Entity" (in Custom Entities tab) opens modal/form with:

```
┌─────────────────────────────────────────────────────┐
│ CREATE CUSTOM ENTITY                                 │
├─────────────────────────────────────────────────────┤
│                                                      │
│ Entity Name: *                                      │
│ [Project_Entity_________________________]           │
│ Character limit: 100                                │
│                                                      │
│ Description:                                        │
│ [Custom entity for project-specific fields____]    │
│ [___________________________________________]       │
│ Character limit: 500                                │
│                                                      │
│ ℹ️ After creating entity, you can add custom       │
│    fields specific to this entity                   │
│                                                      │
│ [Cancel] [Create Entity]                            │
└─────────────────────────────────────────────────────┘
```

**AC2: Entity Name Validation**

- Required field
- Must be unique (case-insensitive)
- Must end with "_Entity" suffix (enforced or auto-appended)
- 100 character maximum
- No special characters except underscore
- Real-time validation with error messages

**AC3: Edit Existing Entity**

Clicking "Edit" on entity card opens similar modal in edit mode:
- Pre-filled with current values
- Entity name NOT editable if entity has fields (prevent breaking references)
- Description editable
- Additional section showing field count: "This entity has 5 fields"

**AC4: Delete Entity Protection**

Entities cannot be deleted if:
- Entity has fields associated with it
- Entity is Core (type = 'CORE')

Delete button disabled with tooltip: "Cannot delete entity with fields. Remove fields first."

For deletable entities (Custom entities with no fields):
- "Delete Entity" button shows
- Confirmation modal: "Delete [Entity Name]? This action cannot be undone."

**AC5: Success/Error Handling**

After create/edit:
- Success toast: "Entity created successfully" or "Entity updated"
- Auto-close modal
- Refresh entity list
- Scroll to newly created entity

Errors:
- Validation errors inline below fields
- API errors in toast notification
- Modal remains open to allow corrections

### Technical Notes

**API:**
```
POST /api/admin/entities
Body: {
  entity_name: string,
  entity_type: 'CUSTOM',
  description: string
}

PUT /api/admin/entities/{entity_id}
Body: {
  description: string  // Name not editable
}

DELETE /api/admin/entities/{entity_id}
Validation: Check field count = 0 before delete
```

**Story Points:** 5

---

## STORY 4: Fields Library UI - Create/Edit Field

**As a** Business Admin  
**I want to** create and configure fields with validation rules and properties  
**So that** I can build the data collection components for the system

### Acceptance Criteria

**AC1: Create Field - Form Interface**

Clicking "+ Add Field" opens modal/page with multi-section form:

**Section 1: Basic Information**
```
Field Name: * (Technical name, e.g., "TenderTitle")
[TenderTitle_____________________________]
ℹ️ Use camelCase without spaces

Display Name: * (Human-readable, e.g., "Tender Title")
[Tender Title_____________________________]

Description:
[Brief description of what this field captures___]
[________________________________________________]
```

**Section 2: Classification**
```
Assign to Entity: * 
[Select Entity                            ▼]
Options: 
  - Tender_Entity
  - Vendor_Entity
  - Pricing_Entity
  - ... (all active entities)
  - [None - Unstructured Field]

Field Tier: (Auto-set based on entity selection)
○ Core Standard Field (if core entity selected)
○ Entity-Specific Custom Field (if core entity selected)
○ Unstructured Custom Field (if no entity selected)
```

**Section 3: Data Type & Validation**
```
Data Type: *
[Select Type                              ▼]
Options: TEXT, NUMERIC, DATE, LOV, BOOLEAN, TABLE, RICH_TEXT

-- IF TEXT selected:
  ☐ Mandatory
  Character Limit: [500____] (optional)
  ☐ Multi-line (textarea vs input)

-- IF NUMERIC selected:
  ☐ Mandatory
  Minimum Value: [0_______] (optional)
  Maximum Value: [999999__] (optional)
  Decimal Places: [2__] (optional)

-- IF DATE selected:
  ☐ Mandatory
  ☐ Must be future date
  ☐ Must be business day

-- IF LOV selected:
  ☐ Mandatory
  List of Values: [Select LOV             ▼]
  Options: Load from list_of_values table
  [+ Create New LOV]

-- IF BOOLEAN selected:
  ☐ Mandatory
  Label for True: [Yes_____]
  Label for False: [No______]
```

**Section 4: Additional Properties**
```
Default Value: (optional)
[_____________________________________________]

Placeholder Text: (shows in empty field)
[Enter tender title..._______________________]

Help Text: (tooltip or helper)
[Provide a clear, concise title for the tender]
[_____________________________________________]

Read-Only For Roles: (multi-select dropdown)
☐ Vendor
☐ Collaborator
☐ Evaluation Panel
(Selected roles can view but not edit this field)
```

**Section 5: Preview**

Live preview showing how field will render:
```
┌─────────────────────────────────────────┐
│ FIELD PREVIEW                            │
├─────────────────────────────────────────┤
│ Tender Title: *                         │
│ [Enter tender title...____________]     │
│ ℹ️ Provide a clear, concise title      │
└─────────────────────────────────────────┘
```

Preview updates in real-time as admin configures

**AC2: Validation Rules Builder**

For complex validation, provide rule builder interface:

```
Validation Rules:
☑ Field is mandatory
☑ Character limit: [500]
☐ Unique value (no duplicates allowed)
☐ Must match pattern: [Regular expression________]

Conditional Validation (advanced):
☐ Required if [OtherField___▼] equals [Value___]
```

**AC3: LOV Management Integration**

If admin selects "Create New LOV" from LOV dropdown:

Opens nested modal:
```
┌─────────────────────────────────────────┐
│ CREATE LIST OF VALUES                    │
├─────────────────────────────────────────┤
│ LOV Name: * [ProcurementMethods_____]   │
│                                          │
│ Values:                                  │
│ Value      | Label                       │
│ [RFP____] | [Request for Proposal___]   │
│ [ITB____] | [Invitation to Bid______]   │
│ [+ Add Value Row]                        │
│                                          │
│ [Cancel] [Create LOV]                    │
└─────────────────────────────────────────┘
```

After LOV created, auto-selects in parent field form

**AC4: Edit Existing Field**

Clicking "⚙️ Edit" on field opens same form pre-populated:
- All values pre-filled
- Field Name NOT editable if field is used in any blocks (prevent breaking references)
- All other properties editable
- "Delete Field" button (if not used anywhere)

**AC5: Field Name Uniqueness Validation**

- Check uniqueness within entity (for entity-grouped fields)
- Check global uniqueness for unstructured fields
- Real-time validation while typing
- Error message: "Field name already exists in [Entity Name]"

**AC6: Dependency Checking Before Delete**

Before allowing field deletion, check:
```
Used in Blocks: 3 (General Information, Bidder Information, Pricing Schedule)
Used in Forms: 1 (Bank Guarantee Form)
Used in Templates: 5 templates
```

If in use:
- Delete button disabled
- Tooltip: "Cannot delete field in use. Field is used in 3 blocks, 1 form, 5 templates"
- Option: "Deprecate Instead" → Changes status to DEPRECATED (still accessible in existing usages but not for new)

If not in use:
- Delete button enabled
- Confirmation: "Delete field [FieldName]? This action cannot be undone."

**AC7: Bulk Actions** (Nice to have - can defer)

Checkbox selection on fields for bulk operations:
- Bulk status change (Active ↔ Deprecated)
- Bulk entity assignment (move fields to different entity)
- Export selected fields (CSV/JSON)

**AC8: Field Cloning**

"Clone" button on existing fields:
- Opens create form pre-filled with source field properties
- Auto-appends "_Copy" to field name
- Admin edits and saves as new field
- Useful for creating similar fields quickly

### Technical Notes

**API Endpoints:**
```
GET /api/admin/fields?entity_id={id}
GET /api/admin/fields?tier={tier}
POST /api/admin/fields
PUT /api/admin/fields/{field_id}
DELETE /api/admin/fields/{field_id}
GET /api/admin/fields/{field_id}/usage-check

GET /api/admin/lov
POST /api/admin/lov
```

**Validation:** Server-side validation mirrors client-side, comprehensive error messages

**UX:** Auto-save draft every 30 seconds (if form partially filled), restore on return

**Story Points:** 13

---

## STORY 5: Criteria Library UI - Criteria Management

**As a** Business Admin  
**I want to** create and manage criteria definitions that link fields to logic rules  
**So that** Policy Engine and Workflow Engine can use these for decision-making

### Acceptance Criteria

**AC1: Criteria Library Page Layout**

Single-page table view with:
- Title: "Criteria Library"
- Subtitle: "Define criteria that drive system logic and decisions"
- Action button: "+ Add Criterion"

**AC2: Criteria Table Display**

Table with columns matching screenshot:
- **Criterion ID:** Code (e.g., `CRIT_PROCUREMENT_METHOD`) displayed as monospace
- **Criterion Name:** Human-readable (e.g., "Procurement Method Check")
- **Source Field:** Field name from Fields Library (e.g., "ProcurementMethod from Tender_Entity")
- **Associated Logic:** Logic type badge (LOV_SELECTION, NUMERIC_THRESHOLD, BOOLEAN_YES_NO_CHECK, DATE_COMPARISON)
- **Status:** Active/Inactive badge
- **Actions:** "⚙️ Edit" button

**AC3: Logic Type Info Panel**

Below table, informational panel explaining logic types:

```
ℹ️ ASSOCIATED LOGIC TYPES

• BOOLEAN_YES_NO_CHECK: Simple true/false validation
  Example: Check if BidSecurityRequired = Yes

• NUMERIC_THRESHOLD: Compare numeric values (>, <, =, >=, <=)
  Example: EstimatedValue > $100,000

• LOV_SELECTION: Match against list of values
  Example: ProcurementMethod IN ('RFP', 'ITB')

• DATE_COMPARISON: Compare dates and time periods
  Example: SubmissionDeadline < CurrentDate + 30 days

• TEXT_MATCH: Pattern matching or contains check
  Example: TenderTitle contains "Emergency"
```

**AC4: Create Criterion - Modal Form**

Clicking "+ Add Criterion" opens modal:

```
┌─────────────────────────────────────────────────────┐
│ CREATE CRITERION                                     │
├─────────────────────────────────────────────────────┤
│                                                      │
│ Criterion Code: *                                   │
│ [CRIT_ESTIMATED_VALUE_________________]             │
│ ℹ️ Use CRIT_ prefix, UPPER_SNAKE_CASE              │
│                                                      │
│ Criterion Name: *                                   │
│ [Estimated Value Threshold Check_______]           │
│                                                      │
│ Source Field: *                                     │
│ [Search fields...                       🔍]         │
│ Selected: EstimatedOverallValue (Tender_Entity)     │
│                                                      │
│ Associated Logic: *                                 │
│ [Select Logic Type                      ▼]         │
│ Selected: NUMERIC_THRESHOLD                         │
│                                                      │
│ ── Logic Parameters (for NUMERIC_THRESHOLD) ──      │
│                                                      │
│ Default Operator:                                   │
│ [Greater Than (>)                       ▼]         │
│                                                      │
│ Default Threshold Value: (optional)                 │
│ [100000___________________________________]         │
│ ℹ️ Can be overridden in individual rules           │
│                                                      │
│ Description:                                        │
│ [Used to check if tender value exceeds threshold]  │
│ [for mandating financial guarantees________]       │
│                                                      │
│ [Cancel] [Create Criterion]                         │
└─────────────────────────────────────────────────────┘
```

**AC5: Source Field Selection**

Searchable dropdown/autocomplete for source field:
- Shows all active fields from Fields Library
- Groups by entity: "Tender_Entity → TenderTitle, EstimatedValue..."
- Displays field data type badge
- Filter by data type (only show numeric fields for NUMERIC_THRESHOLD logic)

**AC6: Logic Type Constraints**

System validates logic type matches source field data type:

| Source Field Data Type | Allowed Logic Types |
|------------------------|---------------------|
| NUMERIC | NUMERIC_THRESHOLD |
| BOOLEAN | BOOLEAN_YES_NO_CHECK |
| LOV | LOV_SELECTION |
| DATE | DATE_COMPARISON, NUMERIC_THRESHOLD (for day differences) |
| TEXT | TEXT_MATCH, LOV_SELECTION |

If admin selects incompatible combination:
- Error message: "Cannot use NUMERIC_THRESHOLD logic with TEXT field type"
- Logic Type dropdown filtered to show only compatible options

**AC7: Logic Parameters Dynamic Form**

Form section changes based on selected Associated Logic:

**For NUMERIC_THRESHOLD:**
- Operator dropdown: >, <, =, >=, <=, !=
- Threshold value input (numeric)

**For LOV_SELECTION:**
- Operator: IN, NOT IN, EQUALS
- Values: Multi-select from field's LOV if applicable, or text input for custom values

**For BOOLEAN_YES_NO_CHECK:**
- Expected value: TRUE, FALSE, or EITHER
- No additional parameters needed

**For DATE_COMPARISON:**
- Operator: BEFORE, AFTER, BETWEEN, EQUALS
- Reference: Fixed date, relative to current date, relative to another field

**For TEXT_MATCH:**
- Match type: CONTAINS, STARTS_WITH, ENDS_WITH, REGEX
- Pattern input

**AC8: Criterion Code Auto-Generation**

"Auto-Generate" button next to Criterion Code field:
- Generates code from Criterion Name
- Example: "Estimated Value Threshold Check" → "CRIT_ESTIMATED_VALUE_THRESHOLD_CHECK"
- Auto-adds "CRIT_" prefix
- Converts to UPPER_SNAKE_CASE
- Checks uniqueness

**AC9: Edit Criterion**

Same form as create, pre-populated
- Criterion Code NOT editable (used in rules)
- Can change logic type if not used in Policy/Workflow Engine
- Warning if criterion used in rules: "This criterion is used in 3 policy rules. Changes may affect rule behavior."

**AC10: Preview/Test Criterion**

"Test Criterion" section at bottom:
```
Test This Criterion:
Enter test value: [250000]
[Test] → Result: TRUE (Value 250000 > Threshold 100000)
```

### Technical Notes

**API:**
```
POST /api/admin/criteria
Body: {
  criterion_code: string,
  criterion_name: string,
  source_field_id: UUID,
  associated_logic: ENUM,
  logic_parameters: {
    operator: string,
    threshold: any,
    ...
  },
  description: string
}

PUT /api/admin/criteria/{criterion_id}
DELETE /api/admin/criteria/{criterion_id}
GET /api/admin/criteria/{criterion_id}/usage-check
POST /api/admin/criteria/{criterion_id}/test
```

**Validation:** Ensure logic type compatibility with field data type

**Story Points:** 13

---

## STORY 6: Tender Document Template Builder - UI Foundation

**As a** Business Admin  
**I want to** see Blocks Library and drag-drop canvas for template assembly  
**So that** I can build Tender Document Templates visually

### Acceptance Criteria

**AC1: Page Layout - Two-Column Design**

Left sidebar (320px width):
- Header: "Blocks Library"
- Search bar for filtering blocks
- Categorized block list (collapsible categories)

Right canvas area (remaining width):
- Header: "Tender Document Template Canvas"
- Template name input
- Empty canvas with drag-drop zone

**AC2: Blocks Library Sidebar - Display All Blocks**

Display all blocks from `blocks` table where `status = 'ACTIVE'`, grouped by type:

**Category 1: Content Blocks (PROVIDED_BY_PROCUREMENT + PUBLIC)**
```
📋 Content Blocks (8 blocks)
├─ 📋 General Information
├─ 🗂️ Project Details  
├─ 🎯 Technical Specifications
├─ 📊 Evaluation Criteria (HYBRID)
├─ 💰 Financial Guarantees
├─ 📝 Scope of Work
├─ 📄 Submission Requirements
└─ 📜 Contract Terms
```

**Category 2: Returnable Forms (COLLECTED_FROM_VENDORS)**
```
✍️ Returnable Forms (3 blocks)
├─ 📝 Bidder Information Form
├─ 📊 Pricing Schedule
└─ 🔧 Technical Response Form
```

**Category 3: Internal Blocks (PROVIDED_BY_PROCUREMENT + INTERNAL)**
```
🔒 Internal Blocks (2 blocks)
├─ 📊 Evaluation Worksheet
└─ 💰 Budget Analysis
```

**Category 4: Special Components**
```
🔧 Special Components (1 block)
└─ 🛒 Product Catalogue Item (BLOCK_CATALOGUE_ITEM)
```

**AC3: Block Display in Sidebar**

Each block shows:
- Icon (based on category/type)
- Block name
- Small description on hover
- Drag handle indicator
- Badges:
  - Block type (PROVIDED / COLLECTED / HYBRID)
  - Visibility (PUBLIC / INTERNAL)

```
┌────────────────────────────────────────┐
│ 📋 General Information                 │
│ Basic tender details                   │
│ [PROVIDED] [PUBLIC]                    │
└────────────────────────────────────────┘
```

**AC4: Search and Filter in Sidebar**

Search bar filters blocks by name
Filter dropdowns:
- Block Type: All / PROVIDED / COLLECTED / HYBRID
- Visibility: All / PUBLIC / INTERNAL

Categories auto-collapse if no blocks match filters

**AC5: Empty Canvas State**

Before any blocks added:
```
┌─────────────────────────────────────────────────────┐
│           Empty Canvas                               │
│              🎨                                      │
│                                                      │
│   Start Building Your Tender Document Template      │
│   Drag blocks from the left to compose template     │
│                                                      │
└─────────────────────────────────────────────────────┘
```

**AC6: Drag and Drop Functionality**

- Blocks draggable from sidebar
- Canvas accepts drops (ondrop, ondragover events)
- Visual feedback during drag (block slightly transparent, cursor changes)
- Drop zones highlighted when hovering over canvas
- Smooth animations

**AC7: Template Header**

Above canvas, template configuration:
```
Template Name: * [RFP for Goods___________________]

Solicitation Type: [RFP                        ▼]
Procurement Type:  [Goods                      ▼]
Granular Category: [IT Equipment               ▼]

Description: [Standard RFP template for goods procurement_]
             [with technical evaluation and financial___]

[Save as Draft] [Publish Template]
```

**AC8: Responsive Design**

- Minimum viewport: 1280px
- Sidebar collapsible (hamburger menu)
- Canvas scrollable if many blocks added
- Works on laptop screens

**AC9: Loading and Error States**

- Skeleton loaders while fetching blocks
- Empty state if no blocks in library: "No blocks available. Contact system administrator."
- Error state if API fails: "Failed to load blocks. [Retry]"

**AC10: Permissions**

Only `ADMIN` or `TEMPLATE_DESIGNER` role can access

### Technical Notes

**API:**
```
GET /api/admin/blocks?status=ACTIVE
GET /api/admin/blocks?type={type}&visibility={visibility}
```

**Frontend:**
- React DnD or native HTML5 drag-drop
- State management for template being built
- Auto-save drafts every 60 seconds

**Story Points:** 8

---

## STORY 7: Tender Document Template Builder - Assemble & Configure Template

**As a** Business Admin  
**I want to** drag blocks into canvas, arrange them, and mark as mandatory/discretionary  
**So that** I can create complete Tender Document Templates

### Acceptance Criteria

**AC1: Drag Block from Sidebar to Canvas**

When block dropped on canvas:
- Block appears as section in canvas
- Positioned at bottom of existing blocks (or at drop location if drop zones implemented)
- Block displays:
  - Section header with block icon and name
  - Collapsible content area showing fields
  - Controls: Mandatory toggle, Move up/down, Delete

**AC2: Dropped Block Display**

Each block rendered as expandable section:

```
┌─────────────────────────────────────────────────────┐
│ 📋 General Information          [M] [▲] [▼] [🗑️]   │
├─────────────────────────────────────────────────────┤
│ Fields in this block:                               │
│ • TenderTitle (TEXT) - Mandatory                    │
│ • ProcurementMethod (LOV) - Mandatory               │
│ • EstimatedOverallValue (NUMERIC) - Mandatory       │
│ • SubmissionDeadline (DATE) - Mandatory             │
│                                                      │
│ 4 fields total                                      │
└─────────────────────────────────────────────────────┘

[M] = Mandatory toggle
[▲] = Move up  
[▼] = Move down
[🗑️] = Delete from template
```

**AC3: Mandatory/Discretionary Toggle**

Each block has toggle switch "Mandatory":
- ON (checked): Block is mandatory in this template
  - Visual indicator: Red left border or "MANDATORY" badge
  - Means: Always included when template is used
- OFF (unchecked): Block is discretionary
  - Visual indicator: Blue left border or "DISCRETIONARY" badge
  - Means: Tender Author can optionally add this block

Toggle behavior:
- Click to toggle state
- State saved to `template_blocks.is_mandatory_in_template`
- Visual update immediate

**AC4: Block Ordering - Move Up/Down**

Arrow buttons to reorder blocks:
- [▲] Moves block up one position
- [▼] Moves block down one position
- Disabled if already at top/bottom
- Updates `template_blocks.display_order`
- Smooth animation during reorder

Alternative: Drag-to-reorder within canvas
- Grab block header to drag
- Drop between other blocks
- Visual drop zones appear between blocks

**AC5: Section Grouping** (Optional - Can defer to later)

Ability to group blocks into sections:
```
Section: GENERAL INFORMATION
├─ General Information block
└─ Project Details block

Section: TECHNICAL
├─ Technical Specifications block
└─ Scope of Work block

Section: EVALUATION
├─ Evaluation Criteria block
└─ Submission Requirements block
```

Stored in `template_blocks.section_group`

**AC6: Block Configuration Panel**

Clicking block opens right-side panel (or modal) with block details:

```
┌─────────────────────────────────────────────────────┐
│ BLOCK CONFIGURATION                     [✕ Close]   │
├─────────────────────────────────────────────────────┤
│ Block: General Information                          │
│ Type: PROVIDED_BY_PROCUREMENT                       │
│ Visibility: PUBLIC                                  │
│                                                      │
│ ── In This Template ──                              │
│                                                      │
│ Status: [✓] Mandatory                               │
│ ℹ️ This block will always be included              │
│                                                      │
│ Display Order: [1____]                              │
│                                                      │
│ Section Group: [General Information     ▼]         │
│                                                      │
│ ── Block Contents (Read-Only) ──                    │
│                                                      │
│ Fields: 4                                           │
│ • TenderTitle (TEXT)                                │
│ • ProcurementMethod (LOV)                           │
│ • EstimatedOverallValue (NUMERIC)                   │
│ • SubmissionDeadline (DATE)                         │
│                                                      │
│ [View Full Block Definition]                        │
│                                                      │
│ [Update] [Cancel]                                   │
└─────────────────────────────────────────────────────┘
```

**AC7: Delete Block from Template**

Clicking 🗑️ removes block from template:
- Confirmation modal: "Remove [Block Name] from template?"
- If confirmed, block removed from canvas
- Block still exists in Blocks Library (not deleted, just removed from this template)
- Can be re-added by dragging again

**AC8: Duplicate Block Detection**

Cannot add same block twice to template:
- If admin tries to drag block already in template
- Visual feedback: "Block already in template" message
- Drop rejected

**AC9: Template Preview Mode**

"Preview Template" button shows read-only view of how template will look when used:
- All blocks in order
- Mandatory indicators
- Field list per block
- Section groupings
- Useful for review before publishing

**AC10: Block Count & Statistics**

Display template statistics:
```
Template Statistics:
• Total Blocks: 12
• Mandatory: 8
• Discretionary: 4
• PUBLIC blocks: 10
• INTERNAL blocks: 2
```

### Technical Notes

**API:**
```
POST /api/admin/templates/{template_id}/blocks
Body: {
  block_id: UUID,
  is_mandatory: boolean,
  display_order: integer,
  section_group: string
}

PUT /api/admin/templates/{template_id}/blocks/{block_id}
Body: {
  is_mandatory: boolean,
  display_order: integer,
  section_group: string
}

DELETE /api/admin/templates/{template_id}/blocks/{block_id}

PUT /api/admin/templates/{template_id}/blocks/reorder
Body: {
  block_orders: [{block_id: UUID, display_order: integer}]
}
```

**State Management:** Track template being built, auto-save draft every 60 seconds

**Drag-Drop:** HTML5 drag-drop API or React DnD library

**Story Points:** 13

---

## STORY 8: Tender Document Template Builder - Template CRUD Operations

**As a** Business Admin  
**I want to** create, save, edit, and manage tender document templates  
**So that** I can build a library of reusable templates

### Acceptance Criteria

**AC1: Create New Template**

"+ Create Template" button in main toolbar
Opens blank template builder with:
- Empty canvas
- Template information form (name, type, category)
- "Save as Draft" and "Publish" buttons

**AC2: Template Information Form**

Required fields before save:
```
Template Name: * [____________________________]
  Validation: Unique, 3-200 characters

Solicitation Type: * [Select              ▼]
  Options: RFP, ITB, RFQ, EOI, PQ, Direct Contracting

Procurement Type: * [Select               ▼]
  Options: Goods, Services, Works, Mixed

Granular Category: [Select                ▼]
  Options: IT Equipment, Office Supplies, Consulting, 
           Construction, etc.

Description: (optional, recommended)
[This template is for standard goods procurement_]
[using RFP method with technical evaluation__]
Character limit: 500
```

**AC3: Save as Draft**

"Save as Draft" button:
- Validates: Template name, Solicitation type, Procurement type required
- Does NOT validate: Blocks added (can save empty template)
- Sets `status = 'DRAFT'`
- Success message: "Template saved as draft"
- Assigns template_id
- Template appears in Templates List with "DRAFT" badge

**AC4: Publish Template**

"Publish Template" button:
- Validates:
  - Template name, types filled
  - At least 1 block added to template
  - At least 1 mandatory block exists
  - At least 1 PUBLIC block exists (vendors need to see something)
- If validation fails, show errors, block publish
- If validation passes:
  - Confirmation modal: "Publish template [Name]? It will be available for tender creation."
  - Sets `status = 'ACTIVE'`
  - Success message: "Template published successfully"
  - Template now appears in active templates list

**AC5: Templates List View**

"/admin/templates" page shows all templates:

Table with columns:
- Template Name
- Solicitation Type
- Procurement Type
- Category
- Blocks Count
- Status (DRAFT / ACTIVE / DEPRECATED)
- Last Modified
- Actions (Edit, Clone, Deprecate, Delete)

Filters:
- Status: All / Draft / Active / Deprecated
- Solicitation Type: dropdown
- Procurement Type: dropdown

**AC6: Edit Existing Template**

Clicking "Edit" on template:
- Loads template builder with all blocks
- Blocks appear in saved order
- Mandatory/discretionary status preserved
- Template info pre-filled
- Can modify blocks, order, mandatory status
- "Update Template" button (replaces "Publish")

**AC7: Clone Template**

"Clone" button creates copy:
- Opens template builder with all blocks from source
- Template name: "[Original Name] - Copy"
- Status: DRAFT
- All block configurations copied
- Admin can modify before saving

**AC8: Deprecate Template**

"Deprecate" action:
- Changes `status` to 'DEPRECATED'
- Template no longer appears in tender creation screens
- Still visible in admin view with "DEPRECATED" badge
- Existing tenders using this template unaffected
- Can be reactivated if needed

**AC9: Delete Template**

Delete only allowed if:
- Status = DRAFT (never published)
- OR no tenders ever created from this template

If conditions not met:
- Delete button disabled
- Tooltip: "Cannot delete template in use. Deprecate instead."

If deletable:
- Confirmation: "Delete template [Name]? This cannot be undone."
- Permanently removes from database

**AC10: Template Metadata Display**

Each template shows:
```
Created by: John Doe
Created: 2025-01-15
Last modified: 2025-02-01
Times used: 23 tenders
Last used: 2025-02-10
```

### Technical Notes

**API:**
```
GET /api/admin/templates
GET /api/admin/templates/{template_id}
POST /api/admin/templates
PUT /api/admin/templates/{template_id}
DELETE /api/admin/templates/{template_id}
PUT /api/admin/templates/{template_id}/publish
PUT /api/admin/templates/{template_id}/deprecate
POST /api/admin/templates/{template_id}/clone
GET /api/admin/templates/{template_id}/usage-stats
```

**Versioning:** Track template versions if modified after publication (optional for MVP, recommended for future)

**Story Points:** 13

---

## STORY 9: Seed Initial Blocks in Database

**As a** System Administrator  
**I want to** have essential blocks pre-populated in database  
**So that** Business Admins can immediately start building templates

### Acceptance Criteria

**AC1: Seed 15-20 Essential Blocks**

SQL seed script includes blocks covering typical tender needs:

**General & Administrative:**
1. General Information (Tender title, method, value, deadlines)
2. Project Details (Project name, funding source, work package)
3. Requisition Details (Requisition ID, items, amounts)
4. Timeline & Deadlines (Key dates and milestones)

**Technical:**
5. Technical Specifications (Requirements and specs)
6. Scope of Work (Detailed scope and deliverables)
7. BLOCK_CATALOGUE_ITEM (UN WEB BUY integration)
8. Technical Requirements with Response (HYBRID - specs + vendor response)

**Evaluation & Commercial:**
9. Evaluation Criteria (Scoring methodology, weights)
10. Pricing Schedule (Vendor pricing submission)
11. Financial Guarantees (Bid security, performance bonds)
12. Submission Requirements (Bid format and submission instructions)

**Legal & Compliance:**
13. Contract Terms (General Conditions of Contract)
14. Declarations & Certifications (Vendor declarations)

**Returnable Forms:**
15. Bidder Information Form (Company details, registration)
16. Technical Proposal Form (Technical response)
17. Commercial Proposal Form (Financial offer)

**Internal:**
18. Evaluation Worksheet (Internal scoring sheet)
19. Budget Analysis (Internal financial analysis)
20. Risk Assessment (Internal risk evaluation)

**AC2: Each Seeded Block Includes**

Complete configuration:
- Unique block_id (UUID)
- Unique block_code (e.g., 'GEN_INFO_001')
- block_name
- block_type (PROVIDED / COLLECTED / HYBRID)
- visibility (PUBLIC / INTERNAL)
- description (clear purpose)
- status = 'ACTIVE'

**AC3: Link Fields to Blocks**

For each block, seed `block_fields` entries linking appropriate fields:

Example for General Information block:
```sql
INSERT INTO block_fields (block_field_id, block_id, field_id, display_order, is_required_in_block, section_name) VALUES
-- General Information block gets tender entity fields
('bf1', 'b1', 'f_tender_title', 1, TRUE, 'Basic Information'),
('bf2', 'b1', 'f_procurement_method', 2, TRUE, 'Basic Information'),
('bf3', 'b1', 'f_estimated_value', 3, TRUE, 'Financial'),
('bf4', 'b1', 'f_submission_deadline', 4, TRUE, 'Timeline'),
('bf5', 'b1', 'f_tender_reference', 5, TRUE, 'Basic Information'),
('bf6', 'b1', 'f_procurement_category', 6, TRUE, 'Classification');
```

**AC4: Seed Forms** (Basic ones for MVP)

Seed 3-5 standard forms:
```sql
INSERT INTO forms (form_id, form_code, form_name, form_purpose) VALUES
('form1', 'BANK_GUARANTEE_FORM', 'Bank Guarantee Form', 'Standard bank guarantee format'),
('form2', 'BID_SECURITY_FORM', 'Bid Security Form', 'Bid security declaration'),
('form3', 'JV_PARTNER_FORM', 'Joint Venture Partner Information', 'JV partner details');
```

Link forms to appropriate fields from unstructured tier

**AC5: Documentation for Seeded Blocks**

Create reference document (Confluence page or PDF) listing:
- All seeded blocks with descriptions
- Fields included in each block
- Intended use cases
- Block type and visibility
- Which entities' fields are used

**AC6: Seed Script Structure**

Script organized logically:
```sql
-- 1. Seed Entities (if not already done in Story 1)
INSERT INTO entities...

-- 2. Seed Core Fields
INSERT INTO fields...

-- 3. Seed LOVs
INSERT INTO list_of_values...

-- 4. Seed Forms
INSERT INTO forms...

-- 5. Seed Blocks
INSERT INTO blocks...

-- 6. Link Fields to Blocks
INSERT INTO block_fields...

-- 7. Link Forms to Blocks (if blocks incorporate forms)
-- handled through block_fields with form_id
```

**AC7: Idempotent Script**

Script can be run multiple times safely:
- Use `INSERT... ON CONFLICT DO NOTHING` or check existence before insert
- Doesn't duplicate data
- Safe for development environment resets

**AC8: Verification Query**

Include verification queries at end:
```sql
-- Verify all blocks have fields
SELECT b.block_name, COUNT(bf.block_field_id) as field_count
FROM blocks b
LEFT JOIN block_fields bf ON b.block_id = bf.block_id
GROUP BY b.block_id, b.block_name
HAVING COUNT(bf.block_field_id) = 0;
-- Should return empty (no blocks without fields)

-- Verify COLLECTED and HYBRID blocks are PUBLIC
SELECT block_name, block_type, visibility
FROM blocks
WHERE (block_type IN ('COLLECTED_FROM_VENDORS', 'HYBRID'))
AND visibility != 'PUBLIC';
-- Should return empty (all are PUBLIC)
```

### Technical Notes

**Seed Data Location:** `database/seeds/02_blocks_seed.sql`

**Execution:** Part of database migration/setup process

**Dependencies:** Requires Fields and Entities to be seeded first (Story 1)

**Story Points:** 5

---

## STORY 10: Block Details View (Read-Only for MVP)

**As a** Business Admin  
**I want to** view complete block definitions and their field composition  
**So that** I understand what each block contains before adding to templates

### Acceptance Criteria

**AC1: "View Block" Action in Blocks Library Sidebar**

Each block in sidebar has "ℹ️" info icon
Clicking opens block details modal/panel

**AC2: Block Details Modal Display**

```
┌─────────────────────────────────────────────────────┐
│ BLOCK DETAILS: General Information     [✕ Close]    │
├─────────────