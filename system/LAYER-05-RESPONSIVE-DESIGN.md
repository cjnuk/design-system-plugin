# LAYER 05: RESPONSIVE DESIGN

**Purpose:** Breakpoint strategy, mobile-first philosophy, and how components adapt across device sizes.

---

## BREAKPOINT SYSTEM

We use standard Tailwind breakpoints with mobile-first approach:

```
xs:    320px   (phones, default)
sm:    640px   (landscape phones, small tablets)
md:    768px   (tablets, large tablets)
lg:    1024px  (desktops)
xl:    1280px  (large desktops)
2xl:   1536px  (very large screens)
```

### Mobile-First Principle

**Always design for mobile first, then enhance for larger screens:**

```tsx
// ✅ CORRECT: Mobile-first
<div class="text-sm md:text-base lg:text-lg">
  {/* Small text on mobile, larger on tablets, even larger on desktop */}
</div>

// ❌ WRONG: Desktop-first
<div class="text-lg sm:text-base xs:text-sm">
  {/* Confusing and not mobile-first */}
</div>
```

---

## RESPONSIVE TEXT SIZING

### Heading Responsive Scale

```tsx
// H1 - Scales from 24px (mobile) to 48px (desktop)
<h1 class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl font-bold">
  Page Title
</h1>

// H2 - Scales from 20px to 36px
<h2 class="text-xl sm:text-2xl md:text-3xl lg:text-4xl font-semibold">
  Section Heading
</h2>

// H3 - Scales from 18px to 24px
<h3 class="text-lg sm:text-xl md:text-2xl font-semibold">
  Subsection
</h3>

// Body Text - 14px mobile, 16px tablet+
<p class="text-sm sm:text-base leading-relaxed">
  Body text with comfortable reading on all devices.
</p>
```

**Benefits:**
- Text is readable on all devices
- Hierarchy is preserved
- No manual media queries needed

---

## RESPONSIVE SPACING

### Padding & Margin Scale

```tsx
// Section padding: 16px mobile, 24px tablet, 32px desktop
<section class="px-4 sm:px-6 md:px-8 py-8 sm:py-12 md:py-16">
  {/* Content */}
</section>

// Gap between items: 12px mobile, 16px tablet, 24px desktop
<div class="grid grid-cols-1 md:grid-cols-2 gap-3 md:gap-4 lg:gap-6">
  {/* Grid items */}
</div>

// Button padding: smaller on mobile, larger on desktop
<button class="px-3 py-2 sm:px-4 sm:py-2 md:px-6 md:py-3">
  Action
</button>
```

**Benefits:**
- Comfortable touch targets on mobile (min 44x44px)
- Proper spacing hierarchy
- No cramped mobile layouts

---

## RESPONSIVE GRID LAYOUTS

### Single to Two Column

```tsx
{/* 1 column on mobile, 2 on tablet, 3 on desktop */}
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
  {items.map(item => (
    <Card key={item.id}>{item.title}</Card>
  ))}
</div>
```

### Sidebar + Content

```tsx
{/* Full-width on mobile, sidebar on desktop */}
<div class="grid grid-cols-1 lg:grid-cols-[300px_1fr] gap-6">
  <aside class="order-2 lg:order-1">
    {/* Sidebar - appears below on mobile */}
  </aside>
  
  <main class="order-1 lg:order-2">
    {/* Main content - appears first on mobile */}
  </main>
</div>
```

### Asymmetric Layout

```tsx
{/* 2:1 ratio on desktop, single column on mobile */}
<div class="grid grid-cols-1 lg:grid-cols-[2fr_1fr] gap-6">
  <main>Main content (2/3 width)</main>
  <aside>Sidebar (1/3 width)</aside>
</div>
```

---

## RESPONSIVE TYPOGRAPHY

### Scaling Font Sizes (Advanced)

Use fluid sizing for smooth scaling across all viewport sizes:

```css
/* In your CSS file */
@supports (font-size: clamp(1rem, 2.5vw, 2rem)) {
  h1 {
    font-size: clamp(1.5rem, 4vw, 3rem);
    /* Minimum: 24px, Preferred: 4% of viewport width, Maximum: 48px */
  }
}
```

---

## RESPONSIVE IMAGES

### Image Sizing

```tsx
{/* Image scales with container */}
<img 
  src="/image.jpg" 
  alt="Description"
  class="w-full h-auto"
/>

{/* Responsive image sizes */}
<img 
  src="/image.jpg"
  srcSet="/image-sm.jpg 640w, /image-md.jpg 1024w, /image-lg.jpg 1536w"
  sizes="(max-width: 640px) 100vw, (max-width: 1024px) 80vw, 1200px"
  alt="Description"
  class="w-full h-auto"
/>

{/* Picture element for art direction */}
<picture>
  <source media="(min-width: 1024px)" srcSet="/image-lg.jpg" />
  <source media="(min-width: 640px)" srcSet="/image-md.jpg" />
  <img src="/image-sm.jpg" alt="Description" class="w-full h-auto" />
</picture>
```

---

## RESPONSIVE COMPONENTS

### Navigation Example

```tsx
export function Navigation() {
  const [isOpen, setIsOpen] = useState(false)

  return (
    <nav class="flex items-center justify-between p-4">
      {/* Logo - always visible */}
      <div class="text-xl font-bold">Logo</div>

      {/* Mobile menu button */}
      <button 
        class="md:hidden"
        onClick={() => setIsOpen(!isOpen)}
      >
        ☰
      </button>

      {/* Navigation links - hidden on mobile, visible on tablet+ */}
      <ul class={`flex gap-6 ${isOpen ? 'block' : 'hidden'} md:flex`}>
        <li><a href="/">Home</a></li>
        <li><a href="/about">About</a></li>
        <li><a href="/contact">Contact</a></li>
      </ul>
    </nav>
  )
}
```

### Form Example

```tsx
{/* Single column on mobile, two columns on desktop */}
<form class="grid grid-cols-1 md:grid-cols-2 gap-4">
  <input placeholder="First Name" />
  <input placeholder="Last Name" />
  
  {/* Full width on both */}
  <input 
    placeholder="Email" 
    class="md:col-span-2"
  />
  
  {/* Button full width on mobile, auto width on desktop */}
  <button class="md:col-span-2 md:w-auto md:ml-auto">
    Submit
  </button>
</form>
```

---

## HIDING/SHOWING ELEMENTS

### Strategic Visibility

```tsx
{/* Show only on mobile */}
<button class="md:hidden">
  Mobile menu
</button>

{/* Hide on mobile, show on tablet+ */}
<div class="hidden md:block">
  Desktop navigation
</div>

{/* Show on small screens and large, hide on medium */}
<div class="md:hidden lg:block">
  Alternative layout
</div>

{/* Show on mobile and tablet, hide on desktop */}
<div class="xl:hidden">
  Mobile-optimized content
</div>
```

### Best Practices
- ✅ Use `hidden` and breakpoints for layout changes
- ❌ Don't show/hide to load different content (SEO penalty)
- ✅ Hide decorative elements on mobile to reduce clutter
- ✅ Show help text on desktop, hide on mobile

---

## TOUCH-FRIENDLY SIZING (MOBILE)

### Minimum Touch Target Size

```tsx
{/* Minimum 44x44px for touch */}
<button class="px-3 py-2.5 text-sm">
  {/* 14px text + 8px padding = 30px, not enough */}
  {/* Solution: Use md: for larger sizing */}
</button>

{/* Better */}
<button class="px-4 py-3 sm:px-3 sm:py-2 text-sm">
  {/* 16px padding mobile = 44x44px, 12px padding tablet = 36px */}
</button>

{/* Explicit touch sizing */}
<button class="min-h-[44px] min-w-[44px]">
  Icon button
</button>
```

---

## RESPONSIVE BEHAVIOR CHECKLIST

When designing a component, ask:

- [ ] Does text size scale appropriately?
- [ ] Are spacing/margins responsive?
- [ ] Is the layout single column on mobile?
- [ ] Are touch targets 44x44px minimum on mobile?
- [ ] Are images responsive (w-full)?
- [ ] Is horizontal scrolling avoided?
- [ ] Does navigation work on mobile (hamburger menu)?
- [ ] Are forms single column on mobile?
- [ ] Are modal/dialogs full-width on mobile?
- [ ] Are sidebars hidden on mobile?

---

## PER-COMPONENT RESPONSIVE BEHAVIOR

### Button
```
Mobile: px-3 py-2 text-sm
Tablet: px-4 py-2 text-sm
Desktop: px-6 py-3 text-base
```

### Card
```
Mobile: full-width, margin bottom (stack)
Tablet: 2 per row, gap-4
Desktop: 3 per row, gap-6
```

### Table
```
Mobile: card view (stack rows), show critical columns only
Tablet: horizontal scroll with some columns hidden
Desktop: full table with all columns
```

### Form
```
Mobile: single column
Tablet: single column
Desktop: two columns for related fields
```

---

## TESTING RESPONSIVE DESIGN

### Browser DevTools
```
1. Open DevTools (F12)
2. Toggle device toolbar (Ctrl+Shift+M)
3. Test at breakpoints: 320px, 640px, 768px, 1024px, 1536px
4. Test real devices when possible
```

### Common Issues
- Text too small on mobile
- Buttons too small to tap
- Images too wide
- Horizontal scrolling appears
- Content overlaps
- Layout breaks at odd widths

### Solution Strategy
1. Build mobile-first (smallest screen)
2. Test each breakpoint
3. Add responsive classes as needed
4. Test on real devices

---

## NEXT STEPS

1. **Test your design** on real mobile devices
2. **Read [LAYER-06-STATE-MANAGEMENT.md](./LAYER-06-STATE-MANAGEMENT.md)** for state handling
3. **Read [LAYER-07-INTERACTION-PATTERNS.md](./LAYER-07-INTERACTION-PATTERNS.md)** for UX patterns
