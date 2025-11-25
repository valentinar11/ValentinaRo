# Rich Text Editor - Debugging & Testing Guide

## Issue: Editor Not Appearing When Clicking "Add Description Field"

### Quick Diagnostics

#### Step 1: Check Browser Console
1. Open your browser's Developer Tools
   - **Chrome/Edge**: Press `F12` or `Ctrl+Shift+I` (Windows) / `Cmd+Option+I` (Mac)
   - **Firefox**: Press `F12` or `Ctrl+Shift+K`
   - **Safari**: `Cmd+Option+C`

2. Go to the **Console** tab

3. Click "Add Description Field" button

4. Look for any **red error messages**

#### Common Errors & Solutions

**Error: "formatTextByCode is not defined"**
- **Cause**: JavaScript function not loaded
- **Solution**: Refresh the page completely (Ctrl+F5 or Cmd+Shift+R)

**Error: "Cannot read property 'innerHTML' of null"**
- **Cause**: Editor element not found in DOM
- **Solution**: Check if `renderCurrentBlock()` is executing properly

**Error: "productRequirements is not defined"**
- **Cause**: Global variable not initialized
- **Solution**: Navigate to "Requisition & Lots" section first to initialize data

### Step 2: Verify Prerequisites

The Rich Text Editor requires these conditions to work:

✅ **1. Navigate to Correct Section**
```
Tender Authoring → Click "Scope & Requirements" in navigation tabs
```

✅ **2. Requisition Lines or Lots Must Exist**
```
Navigate to "Requisition & Lots" section first
→ Either:
   a) Requisition lines loaded from ERP (auto-initialized), OR
   b) Create at least one lot with products
```

✅ **3. UNSPSC Codes Available**
```
In "Product Requirements & Specifications"
→ Should see list of products (UNSPSC codes)
→ If empty, go back to step 2
```

✅ **4. Click the Right Button**
```
For each UNSPSC code, you'll see TWO buttons:
→ [📋 Add Attributes] ← Creates structured attributes
→ [📝 Add Description Field] ← Opens rich text editor ✓
```

### Step 3: Testing Flow

#### Complete Test Procedure

1. **Start Fresh**
   ```
   1. Refresh page (Ctrl+F5)
   2. Open browser console (F12)
   3. Start from beginning of tender authoring
   ```

2. **Navigate to Product Requirements**
   ```
   1. Click "Scope & Requirements" tab
   2. Scroll to "Product Requirements & Specifications" section
   3. You should see products listed (e.g., "43211503 - Printer")
   ```

3. **Click Add Description Field**
   ```
   1. Click [📝 Add Description Field] button for any product
   2. Editor should appear immediately
   3. Check console for errors
   ```

4. **Verify Editor Appears**
   ```
   Should see:
   ✓ Formatting toolbar with [B][I][U][S] etc.
   ✓ White content area with placeholder text
   ✓ Footer showing "Words: 0  Characters: 0"
   ```

5. **Test Basic Functionality**
   ```
   1. Click inside editor
   2. Type some text
   3. Word count should update
   4. Try [B] button - text should become bold
   ```

### Step 4: Manual Debugging

#### Check if Editor HTML is Rendered

1. **Open Elements/Inspector Tab** (in Developer Tools)

2. **Look for the editor element**:
   ```html
   <div id="rich-text-editor-43211503" 
        class="rich-text-editor" 
        contenteditable="true">
   ```

3. **If found but not visible**:
   - Check CSS: Look for `display: none` or `visibility: hidden`
   - Check parent containers

4. **If not found at all**:
   - The render condition isn't being met
   - Check `productRequirements` state

#### Check productRequirements State

1. **In Console tab, type**:
   ```javascript
   productRequirements
   ```

2. **Should see object like**:
   ```javascript
   {
     "43211503": {
       requirementType: "description",
       description: "",
       attributes: []
     }
   }
   ```

3. **If empty `{}`**:
   - Product requirements not initialized
   - Need to trigger initialization

#### Force Initialize (Debug Mode)

**In Console, try**:
```javascript
// Check current state
console.log('productRequirements:', productRequirements);

// Force add description for a specific UNSPSC code (use your actual code)
addDescriptionField('43211503');

// Check if it worked
console.log('After addDescriptionField:', productRequirements);
```

### Step 5: Common Issues & Fixes

#### Issue: "Nothing happens when I click button"

**Possible Causes:**

1. **JavaScript not loaded**
   - Solution: Hard refresh (Ctrl+F5)
   - Check console for syntax errors

2. **Button onclick not firing**
   - Right-click button → Inspect Element
   - Check if `onclick="addDescriptionField('...')"` exists
   - Try clicking in console: `addDescriptionField('43211503')`

3. **Wrong view active**
   - Make sure you're in "Authoring View" not "Wizard View"
   - Should see navigation tabs at top

#### Issue: "Editor appears but is empty/broken"

**Possible Causes:**

1. **CSS not loaded**
   - Check Network tab for failed CSS requests
   - Refresh page

2. **contenteditable not working**
   - Check browser compatibility
   - Try different browser

3. **Toolbar buttons not working**
   - Check console for errors when clicking buttons
   - Verify `formatTextByCode` function exists

#### Issue: "Word count shows NaN or doesn't update"

**Possible Causes:**

1. **Word count elements not found**
   - Check if `word-count-43211503` element exists
   - Verify ID sanitization is working

2. **Update function not called**
   - Should trigger on `oninput` and `onblur`
   - Test by typing and clicking outside

### Step 6: Browser Compatibility Check

| Browser | Version | Status |
|---------|---------|--------|
| Chrome | 90+ | ✓ Fully Supported |
| Edge | 90+ | ✓ Fully Supported |
| Firefox | 88+ | ✓ Fully Supported |
| Safari | 14+ | ✓ Fully Supported |
| IE 11 | - | ✗ Not Supported |

**contentEditable Requirements:**
- Modern browser with HTML5 support
- JavaScript enabled
- Cookies/Local Storage enabled (for auto-save)

### Step 7: Network Issues

#### Check for Resource Loading Failures

1. **Open Network tab** in Developer Tools

2. **Refresh page**

3. **Look for failed requests** (red items)
   - CSS files
   - JavaScript files
   - Font files

4. **If resources fail to load**:
   - Check internet connection
   - Check if files exist
   - Check CORS issues

### Step 8: Minimal Test Case

**Create a simple test**:

1. Open browser console

2. Paste this code:
```javascript
// Test if functions exist
console.log('addDescriptionField exists?', typeof addDescriptionField);
console.log('renderCurrentBlock exists?', typeof renderCurrentBlock);
console.log('formatTextByCode exists?', typeof formatTextByCode);

// Test if variables exist
console.log('productRequirements exists?', typeof productRequirements);
console.log('templateBlocks exists?', typeof templateBlocks);

// All should return 'function' or 'object', not 'undefined'
```

### Step 9: Screenshot Examples

#### What You SHOULD See (Working):

```
┌──────────────────────────────────────────────────┐
│ Product Requirements & Specifications             │
├──────────────────────────────────────────────────┤
│ 43211503 - Printer                               │
│                                                   │
│ [TOOLBAR: B I U S | H1 H2 ¶ | • List...]       │
│ ┌────────────────────────────────────────────┐  │
│ │ [Click here to type...]                     │  │
│ │                                             │  │
│ └────────────────────────────────────────────┘  │
│ Words: 0    Characters: 0                        │
└──────────────────────────────────────────────────┘
```

#### What You SHOULD NOT See (Broken):

```
┌──────────────────────────────────────────────────┐
│ Product Requirements & Specifications             │
├──────────────────────────────────────────────────┤
│ 43211503 - Printer                               │
│                                                   │
│ [📋 Add Attributes]  [📝 Add Description Field]  │
│                                                   │
│ ← Still showing buttons = Editor not opening     │
└──────────────────────────────────────────────────┘
```

### Step 10: Get Help

If none of the above works:

1. **Copy Console Errors**
   - Screenshot or copy all red error messages
   
2. **Check Browser & Version**
   - Help → About Browser

3. **Export Debug Info**:
   ```javascript
   // Run in console
   console.log({
     browser: navigator.userAgent,
     productRequirements: productRequirements,
     currentBlock: templateBlocks[currentBlockIndex],
     functionsExist: {
       addDescriptionField: typeof addDescriptionField,
       formatTextByCode: typeof formatTextByCode,
       renderCurrentBlock: typeof renderCurrentBlock
     }
   });
   ```

4. **Share**:
   - Console errors
   - Debug info from above
   - Browser/version
   - Steps you tried

## Quick Fix Checklist

Before reporting an issue, try these:

- [ ] Hard refresh page (Ctrl+F5 or Cmd+Shift+R)
- [ ] Clear browser cache
- [ ] Try different browser
- [ ] Check JavaScript is enabled
- [ ] Check console for errors
- [ ] Navigate to "Requisition & Lots" first to initialize data
- [ ] Verify you're clicking the right button (📝 Add Description Field)
- [ ] Make sure you're in "Scope & Requirements" section
- [ ] Check that UNSPSC codes are visible in the list

## Expected Behavior Summary

```
User Action              →  Expected Result
───────────────────────────────────────────────────
Click "Add Description"  →  Toolbar appears
                            Editor area appears
                            Word count shows "0"
                            Cursor in editor

Type text                →  Text appears
                            Word count updates
                            Auto-saves

Click [B]                →  Selected text becomes bold

Press Ctrl+B             →  Selected text becomes bold

Click [🔗 Link]         →  Prompt for URL appears
```

## Still Not Working?

### Nuclear Option (Complete Reset)

1. Close all browser tabs
2. Clear browser cache and cookies
3. Reopen browser
4. Navigate directly to tender-create.html
5. Follow testing flow from Step 3 above

If this doesn't work, there may be a JavaScript syntax error in the file that needs to be fixed.

