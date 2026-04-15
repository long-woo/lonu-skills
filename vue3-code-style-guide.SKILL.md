---
name: code-style-guide
description: "Use when: writing new code, refactoring components, ensuring consistent formatting, creating Vue/TypeScript files, styling with Tailwind CSS."
---

# Code Style Guide

A comprehensive skill for maintaining consistent code style across Vue 3 + TypeScript projects based on chinaone-ui-libs and brms-ui standards.

## Code Formatting

### Prettier Configuration
- **No semicolons**: `semi: false`
- **Single quotes**: Use single quotes for strings (`'string'`)
- **Arrow functions**: No parentheses for single parameters (`x => x * 2` not `(x) => x * 2`)
- **Print width**: Standard 80 chars (can extend to 100 for complex expressions)
- **Tailwind CSS plugin**: Enable automatic class sorting and organization

```typescript
// ✓ Correct
import { defineComponent } from 'vue'

const getValue = x => x.property
const message = 'Hello World'

// ✗ Avoid
const getValue = (x) => x.property;
const message = "Hello World";
import { defineComponent } from "vue";
```

## TypeScript/JavaScript

### Imports and Exports
- Use explicit named imports
- Group imports: Vue → external libraries → project modules → types
- Import types using `import type` for type-only imports

```typescript
// ✓ Correct order
import { ref, computed } from 'vue'
import { NButton, NModal } from 'naive-ui'
import type { DataTableColumns } from '@co/ui'
import { CoButton, CoModal } from '@co/ui'
import type { UserViewModel } from '@co/api/types'
import { postUserQuery } from '@co/api'
import { useBreadcrumb } from '@co/use'

// Path aliases for monorepo
import { something } from '@co/use'
import { component } from '@/components'
```

### Type Declarations
- Always define Props interfaces explicitly
- Use `withDefaults` for default values
- Export component name in options object
- Extend framework types when needed

```typescript
export interface CoModalProps
  extends /* @vue-ignore */ Omit<ModalProps, 'show' | 'preset'> {
  confirmText?: string
  cancelText?: string
  beforeConfirm?: boolean | (() => boolean | Promise<boolean>)
  loading?: boolean
  showFooter?: boolean
}

export default {
  name: 'CoModal',
  inheritAttrs: false,
}

const props = withDefaults(defineProps<CoModalProps>(), {
  preset: 'card',
  confirmText: '确定',
  cancelText: '取消',
  beforeConfirm: true,
  showFooter: true,
})
```

### Function Declarations
- Use arrow functions for callbacks and utilities
- Use regular functions in Vue options
- Use async/await instead of .then() chains

```typescript
// ✓ Correct
const getProjectData = async (isPush?: boolean) => {
  loading.value = true

  const res = await postProjectQuery(searchForm.value)

  loading.value = false
  if (!res.success) return
  // ...
}

// Lifecycle hooks and methods
const onConfirm = async () => {
  // ...
}

const onClick = () => {
  // ...
}
```

## Vue 3 Components

### Component Structure
- Use `<script setup lang="ts">` for most components
- Use `<script lang="ts">` separately for name and type exports
- Keep template separate and clean

```vue
<script lang="ts">
export default {
  name: 'CoIconButton',
  inheritAttrs: false,
}

export interface CoIconButtonProps extends ButtonProps {
  icon: IconPropName
  tooltip?: string
  iconClass?: string
}
</script>

<script lang="ts" setup>
import type { ButtonProps } from 'naive-ui'
import { NButton, NTooltip } from 'naive-ui'
import type { IconPropName } from '../icon/Icon.vue'
import CoIcon from '../icon/Icon.vue'

defineProps<CoIconButtonProps>()
</script>

<template>
  <NTooltip :disabled="!tooltip">
    <template #trigger>
      <NButton text v-bind="$attrs">
        <CoIcon :class="iconClass" :name="icon" />
      </NButton>
    </template>
    {{ tooltip }}
  </NTooltip>
</template>
```

### Reactive State Management
- Use `ref` for primitive or simple object states
- Use `computed` for derived values
- Type reactive refs explicitly

```typescript
const projectData = ref<ProjectViewModel[]>([])
const searchForm = ref<ProjectQuery>({
  page: 1,
  size: 10,
})
const total = ref(0)
const loading = ref(false)

const pageCount = computed(() => Math.ceil(total.value / searchForm.value.size))
```

### defineProps, defineEmits, defineModel
- Use generic syntax with `<>` for type safety
- Use `withDefaults` for optional props with defaults
- Always type emits explicitly

```typescript
// Props
const props = defineProps<CoModalProps>()
const props = withDefaults(defineProps<CoModalProps>(), {
  preset: 'card',
  confirmText: '确定',
})

// Model
const show = defineModel<boolean>('show')

// Emits
const emits = defineEmits<{
  (e: 'confirm'): void
  (e: 'cancel'): void
}>()
```

### Component Format (TSX)
- Use TSX for complex rendering logic
- Keep render functions readable
- Use arrow functions for inline handlers

```tsx
<script lang="tsx" setup>
import { ref } from 'vue'

const render = () => (
  <div class="flex items-center">
    <button onClick={() => onEdit(row.id)}>Edit</button>
  </div>
)
</script>

<template>
  <div>{{ render() }}</div>
</template>
```

## Styling

### Tailwind CSS
- Use Tailwind utility classes for styling
- Apply Tailwind plugin for automatic class sorting
- Prefer utility classes over custom CSS when possible
- For complex layouts, use Tailwind `@apply` directive

```vue
<template>
  <div class="flex items-center justify-between gap-4">
    <span class="text-sm text-gray-500">{{ title }}</span>
    <button class="rounded-md bg-blue-500 px-4 py-2 text-white hover:bg-blue-600">
      Click
    </button>
  </div>
</template>
```

### SCSS Variables and Mixins
- Use SCSS only for CSS variables and mixins
- Import shared variables from theme files
- Keep component styles scoped

```vue
<style scoped lang="scss">
@import '@/theme/var.scss'

.component {
  color: var(--primary-color)
  
  &:hover {
    color: var(--primary-hover)
  }
}
</style>
```

## Component Naming

### File and Component Names
- Use PascalCase for Vue component files
- Prefix reusable UI components with "Co" (e.g., `CoModal.vue`, `CoButton.vue`)
- Use descriptive names for view components (e.g., `HomeView.vue`, `IndexView.vue`)
- Use camelCase for utility components and composables

```
components/
  modal/
    Modal.vue              # Core modal
    AsyncModal.vue         # Modal with async handling
  project/
    CreateProject.vue      # Project creation
    UpdateProject.vue      # Project update
composables/
  useProjectData.ts
  useBreadcrumb.ts
```

### Props and Emits Naming
- Use descriptive names reflecting purpose
- Prefix event handlers with lowercase `on` (e.g., `onConfirm`, `onCancel`)
- Use full words, avoid abbreviations

```typescript
// ✓ Correct
const onSave = () => {}
const onValidAndSave = () => {}
const onClick = () => {}

// ✗ Avoid
const save = () => {}
const handleConfirm = () => {}  // Use onConfirm instead
```

## API Client

Generated API clients should follow:
- Function names reflect the HTTP method and endpoint (e.g., `postUserQuery`, `getUserDetail`)
- Always include JSDoc comments with parameters and return types
- Type all parameters and responses

```typescript
/**
 * /auth/api/user/query
 *
 * @param {UserQuery} userQuery - User query parameters
 * @param {ApiClientConfig['config']} [config] - Optional config
 * @returns {Promise<UserInfoModelPagedResultResponse>}
 */
export const postUserQuery = (
  userQuery: UserQuery,
  config?: ApiClientConfig['config']
): Promise<UserInfoModelPagedResultResponse> =>
  fetchRuntime<UserInfoModelPagedResultResponse>(
    '/auth/api/user/query',
    'POST',
    { body: userQuery },
    config
  )
```

## File Organization

### Directory Structure
```
src/
  components/
    atomic/              # Single-purpose components
    compound/            # Composite components
    layout/              # Layout components
  views/
    index/
      IndexView.vue
      CreateView.vue
      UpdateView.vue
      DetailView.vue
  composables/
    useAccess.ts
    useProjectData.ts
  router/
    index.ts
  store/
    index.ts
  types/
    index.ts
  utils/
    helper.ts
  assets/
    images/
    styles/
```

### Module Exports
- Export everything from index.ts files
- Use barrel exports for easy importing
- Group related exports together

```typescript
// components/index.ts
export { default as CoModal } from './modal/Modal.vue'
export { default as CoButton } from './button/Button.vue'
export type { CoModalProps, CoButtonProps } from './types'
```

## Linting and Validation

### Tools Used
- **ESLint**: Enforces code quality rules
- **Prettier**: Handles code formatting automatically
- **StyleLint**: Validates CSS/SCSS
- **Lefthook**: Git hooks for pre-commit validation
- **commitlint**: Validates commit message format

### Pre-commit Checks
- ESLint with `--fix` auto-fixes issues
- Prettier formatting
- Type checking with `vue-tsc`

## Testing (Vitest)

- Test files should be named `*.spec.ts` or `*.test.ts`
- Place tests in `__tests__` directory alongside source
- Use descriptive test names describing behavior

```typescript
// __tests__/Component.spec.ts
describe('CoButton', () => {
  it('should emit click event when clicked', () => {
    // ...
  })

  it('should disable button when loading is true', () => {
    // ...
  })
})
```

## Common Patterns

### Async Modal with Actions
```tsx
<script setup lang="tsx">
const props = withDefaults(
  defineProps<{
    action?: () => Promise<boolean>
  }>(),
  {
    showSuccess: true,
  }
)

const loading = ref(false)

const onSave = async () => {
  if (props.action) {
    loading.value = true
    const res = await props.action()
    loading.value = false
    if (!res) return false
  }
  return true
}
</script>
```

### Data Table with Pagination
```tsx
const columns: DataTableColumns<T> = [
  {
    key: 'action',
    title: '操作',
    width: 120,
    render(row) {
      return (
        <div class="flex gap-2">
          <CoIconButton icon="edit" onClick={() => onEdit(row.id)} />
        </div>
      )
    },
  },
]
```

### Composable Hook Pattern
```typescript
export const useProjectData = () => {
  const projectData = ref<ProjectViewModel[]>([])
  const loading = ref(false)

  const fetch = async () => {
    loading.value = true
    const res = await postProjectQuery()
    loading.value = false
    if (res.success) {
      projectData.value = res.data
    }
  }

  return {
    projectData,
    loading,
    fetch,
  }
}
```

## References

- [Vue 3 Script Setup](https://vuejs.org/api/sfc-script-setup.html)
- [TypeScript for Vue](https://vuejs.org/guide/typescript/overview.html)
- [Tailwind CSS](https://tailwindcss.com)
- [Prettier Docs](https://prettier.io/docs/en/index.html)
- [ESLint Vue Plugin](https://eslint.vuejs.org/)
