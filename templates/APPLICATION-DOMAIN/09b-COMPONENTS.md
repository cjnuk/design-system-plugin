# Component Inventory

Document all components in your application, both installed from shadcn/ui and custom components.

## Installed Components

Components installed from shadcn/ui or other UI libraries:

- Button
- Card
- Dialog
- Form
- Input
- Label
- Select

## Custom Components

Document custom components built for this application.

### Example: DataTableAdvanced

**Purpose**: Advanced data table with sorting, filtering, pagination

**Location**: `src/components/ui/data-table-advanced.tsx`

**Props Interface**:
```typescript
interface DataTableAdvancedProps<T> {
  data: T[]
  columns: ColumnDef<T>[]
  onRowClick?: (row: T) => void
}
```

**Accessibility**: WCAG AA compliant, keyboard navigation supported

**Related**: Uses `@tanstack/react-table` and design tokens from LAYER-01

### Example: FileUploader

**Purpose**: Multi-file upload component with progress

**Location**: `src/components/ui/file-uploader.tsx`

**Features**:
- Drag and drop support
- Progress indication
- File type validation
- Error handling

---

## Implementation Notes

- All components use semantic tokens (LAYER-01)
- CVA variants where applicable (LAYER-02)
- Follow accessibility standards (LAYER-08)
- TypeScript with strict mode enabled
