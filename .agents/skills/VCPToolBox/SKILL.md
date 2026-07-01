```markdown
# VCPToolBox Development Patterns

> Auto-generated skill from repository analysis

## Overview
This skill provides a comprehensive guide to the development patterns, coding conventions, and workflows used in the VCPToolBox repository. The project is primarily JavaScript-based, with a focus on modular dashboard features and documentation management. It employs consistent file naming, import/export styles, and includes repeatable workflows for both feature development and documentation updates.

## Coding Conventions

### File Naming
- **Style:** kebab-case for files (e.g., `cpu-card.vue`, `builtin-cards.ts`)
- **Example:**  
  ```
  src/components/dashboard/cpu-card.vue
  src/dashboard/core/builtin-cards.ts
  ```

### Import Style
- **Alias-based imports** are used for clarity and modularity.
- **Example:**
  ```javascript
  import { CpuCard } from '@/components/dashboard/cpu-card'
  import * as api from '@/api/dashboard'
  ```

### Export Style
- **Named exports** are preferred.
- **Example:**
  ```javascript
  // In builtin-cards.ts
  export const CpuCard = { /* ... */ }
  export function getCardData() { /* ... */ }
  ```

### Commit Patterns
- **Type:** Freeform messages, often short (average 4 chars).
- **Prefix:** None enforced.

## Workflows

### Update Dashboard Card Feature
**Trigger:** When you want to add or optimize a dashboard card (e.g., CpuCard, CalendarCard, DreamReviewCard) in the admin panel.  
**Command:** `/update-dashboard-card`

1. **Edit or Add Vue Component**
   - Create or modify a card component in `src/components/dashboard/`  
     _Example:_ `src/components/dashboard/cpu-card.vue`
2. **Update Core Dashboard Files**
   - Update or add entries in `src/dashboard/core/builtin-cards.ts` and `src/dashboard/core/builtin-component-map.ts`
   - _Example:_
     ```javascript
     // builtin-cards.ts
     export const builtinCards = { CpuCard, CalendarCard }
     ```
3. **Modify Related API Files**
   - Update or add API endpoints in `src/api/*.ts`, `routes/admin/*.js`, or `routes/*.js`
4. **Update Composables or Types (if needed)**
   - Adjust `useDashboardState.ts` or `types/api.*.ts` for new data structures or logic
5. **Update or Add Documentation**
   - Document the new/updated feature in `docs/`, such as `DOCUMENTATION_INDEX.md` or a feature-specific markdown file
6. **Build the Project**
   - Run the build process to generate updated assets in `dist/assets/js` and `dist/assets/css`
7. **Update HTML**
   - Ensure `dist/index.html` reflects any necessary changes

### Update Documentation Image
**Trigger:** When you need to add or correct an image in the documentation.  
**Command:** `/update-doc-image`

1. **Add or Replace Image**
   - Place the new or corrected image in `docs/image/` (supports `.jpg` and `.png`)
2. **Commit the Change**
   - If the image was incorrect or needs further adjustment, repeat as necessary

## Testing Patterns

- **Framework:** Unknown (not detected)
- **Test File Pattern:** Test files are named with the pattern `*.test.*`
  - _Example:_ `cpu-card.test.js`
- **General Practice:** Place test files alongside source files or in a dedicated test directory, following the naming convention.

## Commands

| Command               | Purpose                                                      |
|-----------------------|--------------------------------------------------------------|
| /update-dashboard-card| Add or optimize a dashboard card feature in the admin panel  |
| /update-doc-image     | Add or update an image in the documentation                  |
```
