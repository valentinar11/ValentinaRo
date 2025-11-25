# Procurement Lifecycle Tracker - Visual Guide

## Component Layout

```
┌──────────────────────────────────────────────────────────────────────┐
│  🔗 Procurement Lifecycle & Related Processes                        │
│                                                    [➕ Link Process]  │
│                                                    [🔄 Refresh]       │
├──────────────────────────────────────────────────────────────────────┤
│                                                                       │
│  VISUAL LIFECYCLE CHAIN                                              │
│  ┌──────┐    ┌──────┐    ┌──────┐    ┌──────┐    ┌──────┐         │
│  │  📋  │ →  │  ❓  │ →  │  📄  │ →  │  📜  │ →  │  📝  │         │
│  │ REQ  │    │ RFI  │    │ RFP  │    │ LTA  │    │ CNT  │         │
│  │ ID#1 │    │ ID#2 │    │ ID#3 │    │ ID#4 │    │ ID#5 │         │
│  └──────┘    └──────┘    └──────┘    └──────┘    └──────┘         │
│  Completed   Completed   Current     Future      Future            │
│   (Green)     (Green)     (Blue)      (Gray)      (Gray)           │
│                                                                       │
├──────────────────────────────────────────────────────────────────────┤
│  LINKED PROCESSES (3)                                                │
│  ┌────────────────────────────────────────────────────────────────┐ │
│  │ Medical Equipment Requisition          [PREDECESSOR] [View] [×]│ │
│  │ 🆔 REQ-2024-001 📋 Requisition 📅 2024-11-15 👤 Sarah Johnson │ │
│  └────────────────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────────────────┐ │
│  │ Request for Information - Suppliers    [PREDECESSOR] [View] [×]│ │
│  │ 🆔 RFI-2024-045 📋 RFI 📅 2024-12-01 👤 Michael Chen          │ │
│  └────────────────────────────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────────────────────────────┐ │
│  │ Long Term Agreement - IT Equipment     [RELATED]     [View] [×]│ │
│  │ 🆔 LTA-2023-012 📋 LTA 📅 2023-06-15 👤 David Kim             │ │
│  └────────────────────────────────────────────────────────────────┘ │
│                                                                       │
├──────────────────────────────────────────────────────────────────────┤
│  AUDIT TRAIL                                                          │
│  • Process created                         by Current User  Feb 1    │
│  • Linked to RFI (RFI-2024-045)           by Current User  Feb 1    │
│  • Linked to REQ (REQ-2024-001)           by System        Feb 1    │
│  • Linked to LTA (LTA-2023-012)           by Current User  Feb 1    │
└──────────────────────────────────────────────────────────────────────┘
```

## Empty State

```
┌──────────────────────────────────────────────────────────────────────┐
│  🔗 Procurement Lifecycle & Related Processes                        │
│                                                    [➕ Link Process]  │
│                                                    [🔄 Refresh]       │
├──────────────────────────────────────────────────────────────────────┤
│                                                                       │
│                              🔗                                       │
│                                                                       │
│                    No linked processes yet                           │
│         Click "Link Process" to connect related                      │
│                   procurement events                                 │
│                                                                       │
└──────────────────────────────────────────────────────────────────────┘
```

## Link Process Modal

```
┌────────────────────────────────────────────────────────┐
│  Link Procurement Process                          [✕] │
│  Search and select a process to link to this tender    │
├────────────────────────────────────────────────────────┤
│                                                         │
│  🔍 [Search by Process ID, title, or type...      ]    │
│                                                         │
│  ┌──────────────────────────────────────────────────┐  │
│  │ Medical Equipment Requisition      [COMPLETED]  │  │
│  │ 🆔 REQ-2024-001 📋 Requisition 💰 $250,000      │  │
│  └──────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────┐  │
│  │ RFI - Medical Equipment Suppliers  [COMPLETED]  │  │
│  │ 🆔 RFI-2024-045 📋 RFI 💰 N/A                   │  │
│  └──────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────┐  │
│  │ School Construction ITB            [PUBLISHED]  │  │
│  │ 🆔 ITB-2025-015 📋 ITB 💰 $1,200,000            │  │
│  └──────────────────────────────────────────────────┘  │
│                                                         │
└────────────────────────────────────────────────────────┘
```

## Relationship Type Selection

After selecting a process, user is prompted:

```
┌────────────────────────────────────────────────────────┐
│  Link "Medical Equipment Requisition" as:              │
│                                                         │
│  1. Predecessor (this process comes after)             │
│  2. Successor (this process comes before)              │
│  3. Related (parallel or associated process)           │
│                                                         │
│  Enter 1, 2, or 3:  [1]                                │
│                                                         │
│  [Cancel]  [Link Process]                              │
└────────────────────────────────────────────────────────┘
```

## Typical Procurement Lifecycle Flow

```
SOURCING PHASE
    ↓
┌─────────────┐
│ Requisition │ ← Business need identified
│  REQ-001    │
└─────────────┘
    ↓
┌─────────────┐
│     RFI     │ ← Gather market information
│  RFI-001    │
└─────────────┘
    ↓
┌─────────────┐
│     EOI     │ ← Identify capable suppliers
│  EOI-001    │
└─────────────┘
    ↓
SOLICITATION PHASE
    ↓
┌─────────────┐
│   RFP/ITB   │ ← Formal solicitation
│  RFP-001    │
└─────────────┘
    ↓
┌─────────────┐
│     LTA     │ ← Long term agreement (optional)
│  LTA-001    │
└─────────────┘
    ↓
┌─────────────┐
│  Contract   │ ← Contract award
│  CNT-001    │
└─────────────┘
    ↓
DELIVERY & CLOSEOUT
```

## Color Legend

### Node Status Colors
- 🟢 **Green** - Completed/Closed processes
- 🔵 **Blue** - Current process (highlighted)
- ⚪ **Gray** - Future/Potential processes
- 🟡 **Yellow** - Draft processes
- 🔴 **Red** - Cancelled processes

### Relationship Type Badges
- 🔵 **Blue Badge** - Predecessor
- 🟢 **Green Badge** - Successor  
- 🟡 **Yellow Badge** - Related

## Icon Reference

### Process Type Icons
- 📋 **Requisition** - Initial request
- ❓ **RFI** - Request for Information
- ✉️ **EOI** - Expression of Interest
- 💰 **RFQ** - Request for Quotation
- 📄 **RFP** - Request for Proposal
- 🏗️ **ITB** - Invitation to Bid
- 📜 **LTA** - Long Term Agreement
- 📝 **Contract** - Contract document
- 📌 **Other** - Other process types

### Action Icons
- ➕ Add/Link process
- 🔄 Refresh view
- 🔍 Search
- ✕ Remove/Close
- 👁️ View details
- ✓ Completed
- 🆔 Process ID
- 📅 Date
- 👤 User
- 💰 Value
- 📊 Status

## User Interaction States

### 1. Hover on Lifecycle Node
```
┌──────┐
│  📄  │ ← Node scales up slightly
│ RFP  │    Shows tooltip with details
│ ID#3 │    Cursor becomes pointer
└──────┘
  ↑↓
```

### 2. Hover on Linked Process Card
```
┌────────────────────────────────────────────────┐
│ Process Title                    [View] [×]    │ ← Border changes to blue
│ Details...                                     │    Box shadow appears
└────────────────────────────────────────────────┘    Moves slightly right
```

### 3. Success Notification
```
                                    ┌────────────────────────────┐
                                    │ ✓ Successfully linked      │
                                    │   RFI (RFI-2024-045)      │
                                    └────────────────────────────┘
                                    Slides in from right →
                                    Auto-dismiss after 3s
```

## Responsive Behavior

### Desktop (> 1280px)
- Full layout as shown above
- Lifecycle chain horizontal scroll
- All elements visible

### Tablet (768px - 1280px)
- Lifecycle chain becomes scrollable
- Cards stack with full width
- Modal adjusts to screen size

### Mobile (< 768px)
- Lifecycle nodes stack vertically
- Simplified card layout
- Touch-optimized interactions

## Accessibility Features

### Keyboard Navigation
- **Tab** - Navigate between interactive elements
- **Enter/Space** - Activate buttons and links
- **Esc** - Close modals
- **Arrow Keys** - Navigate lifecycle nodes

### Screen Reader Support
- Descriptive ARIA labels
- Process status announcements
- Action confirmations
- Relationship type descriptions

### Visual Indicators
- High contrast colors
- Clear focus states
- Distinctive icons
- Status badges

## Animation Timeline

### Opening Search Modal
```
0ms   - Modal fade in starts (opacity 0 → 1)
150ms - Modal content slides down
300ms - Animation complete
```

### Adding Link
```
0ms   - Link button disabled, shows loading
500ms - Process added to list (fade in)
600ms - Lifecycle chain updates
700ms - Success notification slides in
3700ms- Notification slides out
```

### Removing Link
```
0ms   - Confirmation prompt
if confirmed:
  100ms - Card fade out
  300ms - List reflows
  400ms - Lifecycle chain updates
  500ms - Info notification
```

## Best Practices for Use

### When to Use Predecessor Links
- Previous RFI or EOI that informed this tender
- Requisition that initiated the process
- Previous iteration or amendment

### When to Use Successor Links
- Planned follow-up tenders
- LTA that will result from this RFP
- Contract to be created after award

### When to Use Related Links
- Parallel tenders for same project
- Similar tenders for comparison
- Joint procurement with other agencies
- Secondary bidding against another agency's LTA

## Common Workflows

### Workflow 1: Creating Linked Tender Chain
1. Create Requisition → Mark as Completed
2. Create RFI → Link to REQ as successor
3. Complete RFI → Mark as Completed
4. Create RFP → Link to RFI as successor
5. Result: REQ → RFI → RFP chain visible

### Workflow 2: Cloning with Auto-Link
1. Clone existing RFP
2. System auto-links original as predecessor
3. New RFP shows in lifecycle chain after original
4. Audit trail shows automatic link

### Workflow 3: Cross-Reference Related Tenders
1. Creating similar tender for different region
2. Manually link to original as "Related"
3. Both tenders show relationship
4. Easy to compare and maintain consistency

