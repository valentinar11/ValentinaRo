# Template Builder Workspace - Testing Guide

## 🧪 How to Test the Enhanced Template Builder

### **Step 1: Open the File**

Open `template-builder-workspace.html` in your browser:
- File location: `unops-pdj-development/tasks/mockups/template-builder/template-builder-workspace.html`
- **Recommended:** Use Chrome or Edge for best results

---

### **Step 2: Drag the General Information Block**

1. Look at the **left sidebar** under "Blocks"
2. Find **"General Information"** (📋 icon)
3. **Click and drag** it to the center canvas
4. **Release** to drop

**Expected Result:**
- Block appears on canvas
- Shows admin configuration view
- Displays all 25 fields in a nice layout

---

### **Step 3: Explore the Configuration View**

You should see:

#### **Block Header:**
- 📋 icon + block name (editable)
- "Block Enabled" checkbox
- Delete button (🗑️)

#### **Basic Information Section:**
- 23 field configuration cards
- Each shows:
  - Field icon (📝, 📄, ▼, etc.)
  - Field label
  - Badges (REQUIRED, CONDITIONAL)
  - Mini preview of how field looks
  - Action buttons (✏️ Edit, 📋 Duplicate, 🗑️ Delete)

---

### **Step 4: Edit a Field**

1. Click the **"✏️ Edit"** button on any field (try "Title" first)
2. A modal opens with 3 sections:

#### **Left Column - Basic Configuration:**
- Change "Field Label"
- Select different "Field Type" from dropdown
- Toggle "Required Field"
- Toggle "Field Enabled"

#### **Right Column - Field-Specific Settings:**
- Set "Placeholder Text"
- Add "Help Text"
- Set "Default Value"
- Configure validation (min/max length)

#### **Bottom Section - Conditional Visibility:**
- Shows current rules (if any)
- Button to add/edit rules (placeholder for now)

3. Make some changes:
   - Change the label to "Tender Title"
   - Add placeholder: "Enter a descriptive title"
   - Add help text: "Use clear, professional language"
   - Set min length: 10
   - Set max length: 200

4. Click **"Save Changes"**

**Expected Result:**
- Modal closes
- Green notification appears: "Field configuration saved successfully"
- Field card updates with new label
- Preview shows new placeholder and help text

---

### **Step 5: Try Different Field Types**

Edit a field and change its type:

1. Click Edit on "Description" field
2. Change "Field Type" to "Dropdown"
3. In "Options (one per line)", enter:
   ```
   Goods
   Services
   Works
   Goods & Services
   ```
4. Save

**Expected Result:**
- Preview changes to show a dropdown
- Options appear in the dropdown

---

### **Step 6: Duplicate a Field**

1. Click the **"📋 Duplicate"** button on any field
2. **Expected Result:**
   - Green notification: "Field duplicated successfully"
   - New field appears at bottom with "(Copy)" suffix
   - All settings copied

---

### **Step 7: Add a New Field**

1. Scroll to bottom of section
2. Click **"➕ Add Field"** button
3. Enter a field name (e.g., "Custom Question")
4. Click OK

**Expected Result:**
- New field appears
- Default type is "text"
- Can immediately edit it

---

### **Step 8: Delete a Field**

1. Click **"🗑️ Delete"** button on any NON-required field
2. Confirm deletion

**Expected Result:**
- Confirmation dialog
- Field disappears
- No error

---

### **Step 9: Test Conditional Fields**

Look at the configuration - several fields show **"CONDITIONAL"** badge:

**Example 1: Sourcing Method**
- Only visible when "Sourcing or Solicitation" = "Sourcing"
- Rule shown in field preview

**Example 2: EPP Fields (3 fields)**
- EPP Authorization Link
- EPP Authorization End Date  
- Scope of EPP Approval
- All only visible when "EPP" = "Yes"

**Example 3: Waiver Field**
- Only visible when "Modality of Bid Receipt" = "Email Submission" OR "Physical Submission"

---

### **Step 10: Test Block Management**

#### **Rename Block:**
1. Click on block name input at top
2. Change to "General Info - Updated"
3. Tab out

**Expected Result:**
- Block name updates
- Tab at top updates

#### **Disable Block:**
1. Uncheck "Block Enabled" checkbox

**Expected Result:**
- Block marked as disabled (could be styled differently)

#### **Delete Block:**
1. Click 🗑️ button at top right
2. Confirm

**Expected Result:**
- Block removed from canvas
- Returns to empty state

---

## 🎨 Visual Examples

### **What You Should See:**

#### **Empty Canvas:**
```
┌─────────────────────────────────────┐
│  🎨                                  │
│  Start Building Your Template       │
│  Drag and drop sections from        │
│  the left sidebar to begin          │
└─────────────────────────────────────┘
```

#### **After Dropping General Info Block:**
```
┌────────────────────────────────────────────────┐
│  📋 General Information         [✓] Enabled 🗑️│
├────────────────────────────────────────────────┤
│  Basic Information Section                     │
│                                                 │
│  ┌──────────────────────────────────────────┐ │
│  │ 📝 Title            [REQUIRED]           │ │
│  │ ┌──────────────────────────────────────┐ │ │
│  │ │ Title *                              │ │ │
│  │ │ [Enter tender title            ]      │ │ │
│  │ │ Provide a clear, descriptive title   │ │ │
│  │ └──────────────────────────────────────┘ │ │
│  │                      ✏️ 📋 🗑️            │ │
│  └──────────────────────────────────────────┘ │
│                                                 │
│  ┌──────────────────────────────────────────┐ │
│  │ 📝 Reference        [MAPPED]             │ │
│  │ (preview)                                 │ │
│  │                      ✏️ 📋 🗑️            │ │
│  └──────────────────────────────────────────┘ │
│                                                 │
│  ... (21 more fields) ...                      │
│                                                 │
│  [➕ Add Field]                                │
└────────────────────────────────────────────────┘
```

#### **Field Edit Modal:**
```
┌──────────────────────────────────────────────────────┐
│  Configure Field                               ✖     │
├──────────────────────────────────────────────────────┤
│  ┌─────────────────────┐  ┌────────────────────────┐│
│  │ Basic Configuration │  │ Field-Specific Settings││
│  │                     │  │                        ││
│  │ Field Label         │  │ Placeholder Text       ││
│  │ [Title        ]     │  │ [Enter tender title  ] ││
│  │                     │  │                        ││
│  │ Field Type          │  │ Help Text              ││
│  │ [📝 Text Input  ▼]  │  │ [...              ]    ││
│  │                     │  │                        ││
│  │ ✓ Required Field    │  │ Validation             ││
│  │ ✓ Field Enabled     │  │ Min: [10] Max: [200]   ││
│  └─────────────────────┘  └────────────────────────┘│
│                                                      │
│  ┌──────────────────────────────────────────────────┐│
│  │ Conditional Visibility                           ││
│  │ No conditional rules set                         ││
│  │ [➕ Add Rules]                                   ││
│  └──────────────────────────────────────────────────┘│
├──────────────────────────────────────────────────────┤
│                        [Cancel] [Save Changes]       │
└──────────────────────────────────────────────────────┘
```

---

## ✅ Success Criteria

After testing, you should be able to:

- [x] Drag General Information block to canvas
- [x] See all 25 fields displayed
- [x] Click Edit and see configuration modal
- [x] Modify field properties and save
- [x] See changes reflected in preview
- [x] Duplicate fields with all settings
- [x] Add new custom fields
- [x] Delete fields (with confirmation)
- [x] Rename block and sections
- [x] Enable/disable blocks
- [x] Delete entire block

---

## 🐛 Known Limitations (Not Bugs)

### **Conditional Logic Builder**
- **Status:** Shows rules summary, but edit button is placeholder
- **Why:** Full rules builder is Phase 3
- **Impact:** Can see existing rules, can't edit them yet

### **Other Blocks**
- **Status:** Still use old system (forms, documents, etc.)
- **Why:** Only General Info block upgraded so far
- **Impact:** Other blocks work but don't have full config UI

### **Preview Mode**
- **Status:** Not implemented yet
- **Why:** Phase 3 feature
- **Impact:** Can't test tender author view yet

### **Export/Import**
- **Status:** Not implemented yet
- **Why:** Phase 3 feature
- **Impact:** Can't save/load templates yet

---

## 🔄 Compare: Before vs After

### **Before (Old System):**
```
1. Drag "General Information" block
2. See simple section with hardcoded fields
3. Click field to change label only
4. Limited configuration options
```

### **After (New System):**
```
1. Drag "General Information" block
2. See full admin configuration view
3. Click Edit to open comprehensive modal
4. Configure:
   - Label, type, required, enabled
   - Placeholder, help text, default
   - Options (for dropdowns)
   - Validation rules
   - Conditional visibility (view summary)
5. Duplicate, delete, add fields
6. Full CRUD operations
```

---

## 💡 Testing Tips

### **Tip 1: Test All Field Types**

Try changing a field to each type and see preview:
- Text → Shows input box
- Textarea → Shows textarea
- Dropdown → Shows select with options
- Checkbox → Shows single checkbox
- Checkbox Group → Shows multiple checkboxes
- Date → Shows date picker
- Search → Shows search input with 🔍 icon
- File → Shows file upload button

### **Tip 2: Test Validation**

For text fields:
1. Set min length to 10
2. Set max length to 50
3. See validation displayed in preview

### **Tip 3: Test Multi-Select**

For dropdown fields:
1. Add options (one per line)
2. Check "Allow Multiple Selection"
3. Preview shows multi-select dropdown

### **Tip 4: Test Required vs Optional**

1. Toggle "Required Field" on/off
2. See red asterisk (*) appear/disappear
3. See "REQUIRED" badge update

---

## 📊 Performance Check

The enhanced system should:
- ✅ Load instantly
- ✅ Render block in < 1 second
- ✅ Open modal in < 500ms
- ✅ Save changes in < 200ms
- ✅ Handle 25 fields smoothly

If you experience lag:
- Check browser console for errors
- Try a different browser
- Clear browser cache

---

## 🎯 Next Testing Phase

After Phase 2 (all 7 blocks implemented), you'll test:
1. Multiple blocks on canvas at once
2. Navigation between block tabs
3. Cross-block conditional logic
4. Template export/import
5. Full preview mode

---

## 📝 Feedback Checklist

When testing, note:
- [ ] Does the UI feel intuitive?
- [ ] Are field previews accurate?
- [ ] Is the modal easy to use?
- [ ] Do notifications work well?
- [ ] Are there any confusing parts?
- [ ] What features are missing?
- [ ] Any bugs or errors?

---

## 🆘 Troubleshooting

### **Block doesn't appear when dropped:**
- Refresh page
- Check browser console for errors
- Try dragging again

### **Modal doesn't open:**
- Check browser console
- Make sure you clicked Edit (✏️) button
- Try clicking on a different field

### **Changes not saving:**
- Check if you clicked "Save Changes"
- Look for notification message
- Try editing again

### **Fields look wrong:**
- Refresh page
- Check if correct field type selected
- Verify options are entered correctly

---

## ✨ Summary

You now have a **fully functional template builder** for the General Information block with comprehensive admin configuration capabilities!

**What works:** Everything for General Info block
**What's next:** 6 more blocks following the same pattern
**Estimated time:** 14-21 days for complete implementation

---

**Happy Testing! 🎉**

If you find any issues or have suggestions, note them down for improvements in the next phases.

