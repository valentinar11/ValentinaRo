# Rich Text Editor - Enhanced Features Guide

## Overview

The **Rich Text Editor** is available in the **Scope & Requirements** section, specifically in the **Product Requirements & Specifications** subsection. When you click **"Add Description Field"** for any product/service (UNSPSC code), a fully-featured rich text editor appears with comprehensive formatting options.

## Access Points

### Location in Interface
```
Tender Authoring
└── Scope & Requirements
    └── Product Requirements & Specifications
        └── [Select UNSPSC Code]
            ├── Add Attributes (structured data)
            └── Add Description Field (rich text) ← Opens Rich Text Editor
```

### When to Use
- **Use Rich Text Editor** for: Services, detailed requirements, complex descriptions, narrative content
- **Use Attributes** for: Goods, structured specifications, key-value pairs, technical specs

## Rich Text Editor Interface

### Full Toolbar Layout
```
┌────────────────────────────────────────────────────────────────────┐
│ [B] [I] [U] [S] │ [H1] [H2] [¶] │ [• List] [1. List] [→] [←] │   │
│                 │               │                              │   │
│  Text Style     │   Headings    │        Lists & Indent       │   │
└────────────────────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────┐
│ [⬅] [⬌] [➡] │ [🔗 Link] [—] [✖ Clear]                          │
│             │                                                    │
│  Alignment  │         Utilities                                 │
└────────────────────────────────────────────────────────────────────┘
```

## Formatting Options

### Text Formatting Group
1. **Bold (B)** - Makes text bold
   - Button: `[B]`
   - Keyboard: `Ctrl+B` (Windows/Linux) or `Cmd+B` (Mac)
   - Usage: Emphasis, important terms, headings in text

2. **Italic (I)** - Makes text italic
   - Button: `[I]`
   - Keyboard: `Ctrl+I` or `Cmd+I`
   - Usage: Emphasis, foreign words, book/document titles

3. **Underline (U)** - Underlines text
   - Button: `[U]`
   - Keyboard: `Ctrl+U` or `Cmd+U`
   - Usage: Key terms, emphasis (use sparingly)

4. **Strikethrough (S)** - Strikes through text
   - Button: `[S]`
   - Keyboard: None
   - Usage: Deleted content, corrections, outdated information

### Heading Styles Group
1. **Heading 1 (H1)** - Main heading
   - Button: `[H1]`
   - Font Size: 18px
   - Usage: Main sections, primary headings

2. **Heading 2 (H2)** - Subheading
   - Button: `[H2]`
   - Font Size: 16px
   - Usage: Subsections, secondary headings

3. **Normal Text (¶)** - Paragraph/normal text
   - Button: `[¶]`
   - Font Size: 14px (default)
   - Usage: Body text, regular content

### Lists & Indentation Group
1. **Bullet List (• List)** - Creates unordered list
   - Button: `[• List]`
   - Usage: Non-sequential items, feature lists

2. **Numbered List (1. List)** - Creates ordered list
   - Button: `[1. List]`
   - Usage: Sequential steps, priorities, rankings

3. **Increase Indent (→)** - Indents text/list to the right
   - Button: `[→]`
   - Keyboard: `Tab`
   - Usage: Nested lists, sub-items, hierarchy

4. **Decrease Indent (←)** - Outdents text/list to the left
   - Button: `[←]`
   - Keyboard: `Shift+Tab`
   - Usage: Reduce nesting level

### Text Alignment Group
1. **Align Left (⬅)** - Left-aligns text
   - Button: `[⬅]`
   - Usage: Default alignment, body text

2. **Align Center (⬌)** - Centers text
   - Button: `[⬌]`
   - Usage: Titles, headings, centered content

3. **Align Right (➡)** - Right-aligns text
   - Button: `[➡]`
   - Usage: Dates, signatures, special formatting

### Utilities Group
1. **Insert Link (🔗 Link)** - Inserts hyperlink
   - Button: `[🔗 Link]`
   - Keyboard: `Ctrl+K` or `Cmd+K`
   - Prompts for URL
   - Usage: External references, documents, websites

2. **Insert Line (—)** - Inserts horizontal rule
   - Button: `[—]`
   - Usage: Section separators, visual breaks

3. **Clear Formatting (✖ Clear)** - Removes all formatting
   - Button: `[✖ Clear]`
   - Confirms before clearing
   - Usage: Reset to plain text, remove unwanted formatting

## Editor Features

### Word & Character Counter
Located at bottom-left of editor:
```
Words: 147        Characters: 892
```
- Updates in real-time as you type
- Helps meet length requirements
- Useful for documentation standards

### Smart Placeholder Text
```
Enter a detailed description of what you want to buy for this 
product/service. You can include specifications, requirements, 
quality standards, etc.
```
- Disappears when you start typing
- Returns if editor is empty
- Provides guidance on content

### Auto-Save
- Content saves automatically on:
  - Input (typing)
  - Blur (clicking outside editor)
  - Format changes
- No manual save required
- Data persists in tender document

## Keyboard Shortcuts

### Essential Shortcuts
| Action | Windows/Linux | Mac | Button |
|--------|---------------|-----|--------|
| Bold | `Ctrl+B` | `Cmd+B` | [B] |
| Italic | `Ctrl+I` | `Cmd+I` | [I] |
| Underline | `Ctrl+U` | `Cmd+U` | [U] |
| Insert Link | `Ctrl+K` | `Cmd+K` | [🔗] |
| Indent | `Tab` | `Tab` | [→] |
| Outdent | `Shift+Tab` | `Shift+Tab` | [←] |

### Navigation Shortcuts
- **Arrow Keys** - Move cursor
- **Home/End** - Start/end of line
- **Ctrl/Cmd + Home/End** - Start/end of document
- **Ctrl/Cmd + A** - Select all

## Use Cases & Examples

### Use Case 1: Service Description
```
Consulting Services for Digital Transformation

The consultant shall provide:
• Strategic planning and roadmap development
• Technical architecture design
• Implementation support
• Knowledge transfer and training

Key Requirements:
1. Minimum 10 years experience in digital transformation
2. Proven track record with UN agencies or NGOs
3. Expertise in cloud technologies and modern web frameworks

Deliverables must include comprehensive documentation and 
executive presentations.
```

### Use Case 2: Technical Requirements
```
Server Specifications

Primary Requirements:
• Processor: Intel Xeon or AMD EPYC (min 16 cores)
• RAM: 64GB DDR4 ECC minimum
• Storage: 2TB NVMe SSD (RAID 1 configuration)
• Network: Dual 10Gb Ethernet ports

The server must be certified for:
1. ISO 27001 compliance
2. Energy Star rating
3. Extended warranty (5 years minimum)

All components must be brand new with manufacturer warranty.
```

### Use Case 3: Scope of Work
```
Objective

Develop and deploy a custom procurement management system 
to streamline tender processes and improve transparency.

Scope of Work

Phase 1: Requirements Gathering
• Stakeholder interviews (2 weeks)
• Current process documentation
• Gap analysis and recommendations

Phase 2: Development (12 weeks)
• System architecture design
• Frontend and backend development
• Database design and implementation
• Integration with existing systems

Phase 3: Testing & Deployment (4 weeks)
• User acceptance testing
• Training and documentation
• Production deployment
• Post-deployment support (3 months)
```

## Visual Formatting Examples

### Formatted Output
When you use formatting, the output looks like this:

**Bold text** for emphasis

*Italic text* for titles or foreign words

<u>Underlined text</u> for key terms

~~Strikethrough text~~ for corrections

# Main Heading

## Subheading

Normal paragraph text

- Bullet point 1
- Bullet point 2
  - Nested bullet
  - Another nested item

1. First item
2. Second item
3. Third item

[Link to document](https://example.com)

---

Horizontal line above

## Best Practices

### DO: Use Formatting Purposefully
✅ Use headings to organize content into sections
✅ Use lists for multiple items or steps
✅ Use bold for important terms or warnings
✅ Use links to reference external documents
✅ Use indentation to show hierarchy

### DON'T: Over-format
❌ Don't use multiple formatting on same text (bold + italic + underline)
❌ Don't use ALL CAPS for emphasis (use bold instead)
❌ Don't overuse colors (not supported intentionally for consistency)
❌ Don't create very long lists (split into categories)
❌ Don't mix ordered and unordered lists unnecessarily

### Content Guidelines
1. **Be Clear**: Write in clear, concise language
2. **Be Specific**: Provide exact requirements and specifications
3. **Be Complete**: Include all necessary details
4. **Be Structured**: Use headings, lists, and paragraphs appropriately
5. **Be Professional**: Maintain formal procurement language

## Common Workflows

### Workflow 1: Creating Detailed Service Description
1. Click "Add Description Field"
2. Add main heading: Select all → Click [H1] → Type "Service Description"
3. Write overview paragraph
4. Add subheading for requirements: Click [H2] → Type "Requirements"
5. Create bullet list: Click [• List] → Add items
6. Add another section: Click [H2] → Type "Deliverables"
7. Create numbered list: Click [1. List] → Add items
8. Review word count
9. Content auto-saves

### Workflow 2: Formatting Existing Text
1. Select text you want to format
2. Click appropriate formatting button
3. Text updates immediately
4. Continue editing or select new text
5. Use keyboard shortcuts for faster formatting

### Workflow 3: Adding Links to Reference Documents
1. Select text that should become a link
2. Click [🔗 Link] or press Ctrl/Cmd+K
3. Enter full URL (e.g., https://example.com/spec.pdf)
4. Click OK
5. Link is created and highlighted in blue

### Workflow 4: Creating Hierarchical Lists
1. Click [• List] or [1. List]
2. Type first item, press Enter
3. Press Tab to indent (create sub-item)
4. Type sub-item, press Enter
5. Press Shift+Tab to outdent (back to main level)
6. Continue building hierarchy

## Technical Details

### Supported HTML Elements
The editor generates clean HTML:
- `<strong>` for bold
- `<em>` for italic
- `<u>` for underline
- `<s>` or `<strike>` for strikethrough
- `<h3>` for Heading 1
- `<h4>` for Heading 2
- `<p>` for paragraphs
- `<ul>` and `<li>` for bullet lists
- `<ol>` and `<li>` for numbered lists
- `<a href="">` for links
- `<hr>` for horizontal rules

### Browser Compatibility
- Chrome/Edge: Full support
- Firefox: Full support
- Safari: Full support
- All modern browsers with `contentEditable` support

### Data Storage
- Content stored as HTML in `productRequirements[unspscCode].description`
- Persists with tender document
- Renders exactly as formatted in editor
- No data loss on refresh or navigation

### Accessibility
- Full keyboard navigation support
- ARIA labels on all buttons
- Screen reader compatible
- Focus indicators visible
- High contrast mode supported

## Troubleshooting

### Issue: Formatting not applying
**Solution**: 
- Ensure text is selected first
- Click inside editor to focus
- Try keyboard shortcut instead
- Refresh if button is unresponsive

### Issue: Link not inserting
**Solution**:
- Select text before clicking Link button
- Ensure URL starts with http:// or https://
- Don't leave URL field empty
- Re-enter if prompt was cancelled

### Issue: Word count not updating
**Solution**:
- Type or paste content
- Click outside editor to trigger save
- Refresh page if counter stuck
- Counter updates on input/blur events

### Issue: Content not saving
**Solution**:
- Click outside editor to trigger save
- Check browser console for errors
- Ensure JavaScript is enabled
- Try re-entering content

### Issue: Can't remove formatting
**Solution**:
- Select formatted text
- Click [✖ Clear] button
- Confirm when prompted
- Or manually delete and retype

## Tips & Tricks

### Productivity Tips
1. **Use keyboard shortcuts** for faster formatting
2. **Plan structure first** with headings and sections
3. **Write in plain text first**, then format
4. **Use lists** for better readability
5. **Add links** to reference documents

### Quality Tips
1. **Keep paragraphs short** (3-5 sentences)
2. **Use headings** to break up long content
3. **Be consistent** with formatting style
4. **Proofread** before finalizing
5. **Use clear, specific language**

### Advanced Tips
1. **Copy/paste from Word** works (formatting may need adjustment)
2. **Tab navigation** maintains editor focus
3. **Multiple undo** supported (Ctrl/Cmd+Z)
4. **Select all** to format entire content
5. **View source** by inspecting element (developers)

## Feature Comparison

### Rich Text Editor vs Attributes

| Feature | Rich Text Editor | Attributes |
|---------|-----------------|------------|
| **Best For** | Services, descriptions | Goods, specs |
| **Structure** | Narrative, paragraphs | Key-value pairs |
| **Formatting** | Full rich text | Plain text |
| **Length** | Unlimited | Concise values |
| **Use Case** | Consulting services | Laptop specs |
| **Examples** | Scope of work | "RAM: 16GB" |

## Status

✅ **FULLY IMPLEMENTED AND ENHANCED**

The rich text editor includes:
- ✅ 20+ formatting options
- ✅ Keyboard shortcuts
- ✅ Word/character counter
- ✅ Auto-save functionality
- ✅ Link insertion
- ✅ Indentation support
- ✅ Text alignment
- ✅ Clear formatting option
- ✅ Responsive toolbar
- ✅ Placeholder guidance
- ✅ Real-time updates
- ✅ Clean HTML output

Ready for immediate use in tender authoring!

