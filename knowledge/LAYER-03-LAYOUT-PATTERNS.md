# LAYER 03: LAYOUT PATTERNS

**Purpose:** Describe responsive layout systems, grid patterns, and how components adapt to different screen sizes.

---

## LAYOUT FUNDAMENTALS

### Mobile-First Philosophy
Start with mobile (320px), then add complexity as screen size increases:

```html
<!-- Default: mobile layout -->
<div class="flex flex-col gap-4">
  <!-- Full-width single column -->
</div>

<!-- Tablet and up: two columns -->
<div class="flex flex-col gap-4 md:flex-row md:gap-6">
  <!-- Responsive grid -->
</div>

<!-- Desktop: three columns -->
<div class="flex flex-col gap-4 md:flex-row lg:gap-8">
  <!-- Additional spacing on larger screens -->
</div>
```

---

## FLEXBOX PATTERNS

### Vertical Stack (Default)
```html
<div class="flex flex-col gap-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```

### Horizontal Row
```html
<div class="flex flex-row gap-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```

### Centered Content
```html
<!-- Center in both axes -->
<div class="flex items-center justify-center h-screen">
  <div>Centered content</div>
</div>

<!-- Center horizontally, space vertically -->
<div class="flex flex-col items-center gap-8">
  <h1>Title</h1>
  <p>Content</p>
</div>
```

### Space Between Items
```html
<div class="flex justify-between items-center">
  <div>Left</div>
  <div>Right</div>
</div>
```

---

## CSS GRID PATTERNS

### Equal Columns
```html
<!-- 2 columns -->
<div class="grid grid-cols-2 gap-4">
  <div>Column 1</div>
  <div>Column 2</div>
</div>

<!-- 3 columns -->
<div class="grid grid-cols-3 gap-4">
  <div>Column 1</div>
  <div>Column 2</div>
  <div>Column 3</div>
</div>
```

### Responsive Grid
```html
<!-- 1 column mobile, 2 on tablet, 3 on desktop -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
  {items.map(item => (
    <Card key={item.id}>{item.title}</Card>
  ))}
</div>
```

### Asymmetric Grid
```html
<!-- Sidebar + Main content -->
<div class="grid grid-cols-1 md:grid-cols-[300px_1fr] gap-6">
  <aside class="bg-slate-100 p-4 rounded-lg">
    Sidebar
  </aside>
  <main>
    Main content
  </main>
</div>
```

---

## COMMON LAYOUTS

### Header + Main + Footer

```html
<div class="flex flex-col min-h-screen">
  <!-- Header -->
  <header class="bg-white border-b">
    {/* Navigation, logo, etc. */}
  </header>

  <!-- Main content (grows to fill space) -->
  <main class="flex-1 container mx-auto py-8">
    {/* Page content */}
  </main>

  <!-- Footer -->
  <footer class="bg-slate-900 text-white py-8">
    {/* Footer content */}
  </footer>
</div>
```

### Sidebar Layout

```html
<div class="flex">
  <!-- Sidebar (fixed width) -->
  <aside class="w-64 bg-slate-100 border-r p-4">
    {/* Sidebar content */}
  </aside>

  <!-- Main content (flexible) -->
  <main class="flex-1 p-8">
    {/* Main content */}
  </main>
</div>
```

### Two-Column with Sidebar

```html
<div class="flex flex-col md:flex-row gap-6">
  <!-- Main content -->
  <main class="flex-1">
    {/* Primary content */}
  </main>

  <!-- Sidebar (sticky on desktop) -->
  <aside class="w-80 md:sticky md:top-4 md:h-fit">
    {/* Related content, filters, etc. */}
  </aside>
</div>
```

### Card Grid

```html
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
  {items.map(item => (
    <Card key={item.id}>
      <CardHeader>
        <h3>{item.title}</h3>
      </CardHeader>
      <CardContent>
        {item.description}
      </CardContent>
      <CardFooter>
        <Button>Action</Button>
      </CardFooter>
    </Card>
  ))}
</div>
```

---

## FORM LAYOUTS

### Single Column Form

```html
<form class="max-w-md mx-auto space-y-4">
  <FormField>
    <Label>Email</Label>
    <Input type="email" />
  </FormField>

  <FormField>
    <Label>Password</Label>
    <Input type="password" />
  </FormField>

  <Button type="submit" class="w-full">
    Sign in
  </Button>
</form>
```

### Two Column Form (Desktop Only)

```html
<form class="grid grid-cols-1 md:grid-cols-2 gap-4">
  <FormField>
    <Label>First Name</Label>
    <Input />
  </FormField>

  <FormField>
    <Label>Last Name</Label>
    <Input />
  </FormField>

  <!-- Full width -->
  <FormField class="md:col-span-2">
    <Label>Email</Label>
    <Input type="email" />
  </FormField>

  <!-- Buttons -->
  <div class="md:col-span-2 flex gap-4 justify-end">
    <Button variant="outline">Cancel</Button>
    <Button type="submit">Save</Button>
  </div>
</form>
```

---

## RESPONSIVE BEHAVIOR

### Text Size Scaling

```html
<!-- Mobile: 16px (base) | Tablet: 18px | Desktop: 20px -->
<h1 class="text-2xl md:text-3xl lg:text-4xl">
  Responsive Heading
</h1>

<!-- Mobile: 14px | Tablet+: 16px -->
<p class="text-sm md:text-base">
  Responsive body text
</p>
```

### Spacing Scaling

```html
<!-- Mobile: 16px | Tablet: 24px | Desktop: 32px -->
<section class="px-4 md:px-6 lg:px-8">
  {/* Content with responsive padding */}
</section>

<!-- Mobile: 16px gap | Tablet+: 24px gap -->
<div class="grid grid-cols-1 md:grid-cols-2 gap-4 md:gap-6">
  {/* Grid with responsive gaps */}
</div>
```

### Display Hiding

```html
<!-- Show on mobile, hide on tablet+ -->
<div class="md:hidden">
  Mobile menu
</div>

<!-- Hide on mobile, show on tablet+ -->
<div class="hidden md:block">
  Desktop menu
</div>

<!-- Show on mobile and desktop, hide on tablet -->
<div class="lg:hidden">
  Mobile/Tablet view
</div>
```

---

## CONTAINER QUERIES (FUTURE)

Container queries allow components to respond to their container size, not viewport:

```css
@container (min-width: 400px) {
  .card {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
}
```

**Why useful:** A component sized at 400px needs different layout than 800px, regardless of viewport.

---

## RESPONSIVE TABLE

```html
<!-- Desktop: full table | Mobile: card view -->
<div class="overflow-x-auto">
  <table class="w-full">
    <thead class="hidden md:table-header-group">
      <tr>
        <th>Name</th>
        <th>Email</th>
        <th>Role</th>
      </tr>
    </thead>
    <tbody>
      {users.map(user => (
        <tr key={user.id} class="block md:table-row border-b md:border-b mb-4 md:mb-0">
          <td class="block md:table-cell before:content-['Name:'] before:font-bold before:mr-2 md:before:hidden">
            {user.name}
          </td>
          <td class="block md:table-cell before:content-['Email:'] before:font-bold before:mr-2 md:before:hidden">
            {user.email}
          </td>
          <td class="block md:table-cell before:content-['Role:'] before:font-bold before:mr-2 md:before:hidden">
            {user.role}
          </td>
        </tr>
      ))}
    </tbody>
  </table>
</div>
```

---

## MODAL/DIALOG LAYOUT

```html
<!-- Backdrop -->
<div class="fixed inset-0 bg-black/50 z-40">
  <!-- Dialog (centered) -->
  <div class="flex items-center justify-center min-h-screen p-4">
    <dialog class="bg-white rounded-lg shadow-lg w-full max-w-md">
      <header class="p-6 border-b">
        <h2>Dialog Title</h2>
      </header>

      <section class="p-6">
        {/* Content */}
      </section>

      <footer class="p-6 border-t flex gap-2 justify-end">
        <Button variant="outline">Cancel</Button>
        <Button>Confirm</Button>
      </footer>
    </dialog>
  </div>
</div>
```

---

## TYPOGRAPHY LAYOUT

```html
<article class="max-w-2xl mx-auto px-4 py-12">
  <header class="mb-8">
    <h1 class="text-4xl font-bold mb-2">Article Title</h1>
    <p class="text-slate-600">Published on Jan 18, 2026</p>
  </header>

  <div class="prose prose-lg">
    <p>First paragraph...</p>
    <h2>Section heading</h2>
    <p>Content...</p>
  </div>
</article>
```

---

## TOUCH-FRIENDLY SPACING (MOBILE)

On mobile, ensure adequate spacing for finger taps:

```html
<!-- Minimum 44x44px for touch targets -->
<button class="px-4 py-3 md:py-2">
  Touch-friendly button
</button>

<!-- Adequate spacing between interactive elements -->
<div class="flex flex-col gap-3 md:gap-2">
  <button>Action 1</button>
  <button>Action 2</button>
</div>
```

---

## SAFE AREA INSETS (Mobile-Specific)

For devices with notches/safe areas:

```html
<!-- Padding for notch on iPhoneX+ -->
<div class="pt-[max(env(safe-area-inset-top),1rem)]">
  {/* Safe from notch */}
</div>

<!-- Bottom safe area for home indicator -->
<footer class="pb-[max(env(safe-area-inset-bottom),1rem)]">
  {/* Safe from home indicator */}
</footer>
```

---

## NEXT STEPS

1. **Read [LAYER-04-CSS-ARCHITECTURE.md](./LAYER-04-CSS-ARCHITECTURE.md)** for CSS organization details
2. **Read [LAYER-05-RESPONSIVE-DESIGN.md](./LAYER-05-RESPONSIVE-DESIGN.md)** for breakpoint details
3. **Apply these patterns** in your component implementations
