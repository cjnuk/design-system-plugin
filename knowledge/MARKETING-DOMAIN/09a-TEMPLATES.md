---
title: "09a: Tailwind UI Marketing Templates"
version: "2.1"
audience: ["developers", "designers", "llm-agents"]
dependencies: ["LAYER-01-DESIGN-TOKENS", "LAYER-03-LAYOUT-PATTERNS"]
last_updated: "2026-01-18"
---

# 09a: TAILWIND UI MARKETING TEMPLATES

**Purpose:** Document all Tailwind UI templates available for marketing sites.

---

## TEMPLATE INVENTORY

### Hero Sections (8 Variants)

| Variant | Description | Best For |
|---------|-------------|----------|
| **Simple Centered** | Text + CTA centered, minimal | Landing pages |
| **Split with Image** | 50/50 text + image | Product launches |
| **With App Screenshot** | Hero + floating app preview | SaaS products |
| **Angled Background** | Diagonal color split | Bold brands |
| **With Logo Cloud** | Hero + partner logos | Trust building |
| **Split with Screenshot** | Text left, screenshot right | Feature focus |
| **Gradient Background** | Smooth color transitions | Modern feel |
| **Video Background** | Full-width background video | High impact |

### Feature Sections (6 Variants)

| Variant | Description | Best For |
|---------|-------------|----------|
| **Simple 3-Column** | Icon + title + description grid | Core features |
| **With Large Icons** | Oversized icons, prominent | Visual focus |
| **Offset Grid** | Alternating left/right layout | Storytelling |
| **Centered 2x2** | 4 features in grid | Balanced layout |
| **With Screenshot** | Features list + product image | Context |
| **Bento Grid** | Mixed-size feature cards | Modern, dynamic |

### Pricing Sections (4 Variants)

| Variant | Description | Best For |
|---------|-------------|----------|
| **Simple Cards** | 3 tiers side by side | Clear comparison |
| **With Feature Comparison** | Detailed table view | Complex products |
| **Single Tier** | One plan highlighted | Simple pricing |
| **Toggle Monthly/Annual** | Interactive switcher | Flexible billing |

### Testimonials (5 Variants)

| Variant | Description | Best For |
|---------|-------------|----------|
| **Simple Centered** | Single quote, centered | Focus quotes |
| **Side by Side** | 2-3 quotes in row | Multiple voices |
| **With Large Avatar** | Photo-focused | Personal connection |
| **Carousel** | Scrolling testimonials | Many quotes |
| **Grid** | Card grid of quotes | Social proof |

### CTA Sections (4 Variants)

| Variant | Description | Best For |
|---------|-------------|----------|
| **Simple Centered** | Headline + button | Clear action |
| **Split with Image** | Text + visual | Compelling |
| **Brand Panel** | Full-width colored | Bold statement |
| **With App Screenshot** | CTA + product preview | Conversion |

### Other Sections

- **FAQ Accordion** — Collapsible Q&A
- **Newsletter** — Email capture form
- **Stats** — Key metrics display
- **Team** — Team member grid
- **Logo Cloud** — Partner/client logos
- **Contact** — Contact form + info

---

## HERO SECTION EXAMPLES

### Simple Centered Hero

```jsx
export function HeroCentered() {
  return (
    <div className="relative isolate px-6 pt-14 lg:px-8">
      <div className="mx-auto max-w-2xl py-32 sm:py-48 lg:py-56">
        <div className="text-center">
          <h1 className="text-4xl font-bold tracking-tight text-slate-900 sm:text-6xl">
            Data to enrich your online business
          </h1>
          <p className="mt-6 text-lg leading-8 text-slate-600">
            Anim aute id magna aliqua ad ad non deserunt sunt. Qui irure qui 
            lorem cupidatat commodo.
          </p>
          <div className="mt-10 flex items-center justify-center gap-x-6">
            <a
              href="#"
              className="rounded-md bg-blue-600 px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-blue-500"
            >
              Get started
            </a>
            <a href="#" className="text-sm font-semibold leading-6 text-slate-900">
              Learn more <span aria-hidden="true">→</span>
            </a>
          </div>
        </div>
      </div>
    </div>
  )
}
```

### Split Hero with Image

```jsx
export function HeroSplit() {
  return (
    <div className="relative bg-white">
      <div className="mx-auto max-w-7xl lg:grid lg:grid-cols-12 lg:gap-x-8 lg:px-8">
        <div className="px-6 pb-24 pt-10 sm:pb-32 lg:col-span-7 lg:px-0 lg:pb-56 lg:pt-48 xl:col-span-6">
          <div className="mx-auto max-w-2xl lg:mx-0">
            <h1 className="text-4xl font-bold tracking-tight text-slate-900 sm:text-6xl">
              Deploy to the cloud with confidence
            </h1>
            <p className="mt-6 text-lg leading-8 text-slate-600">
              Anim aute id magna aliqua ad ad non deserunt sunt.
            </p>
            <div className="mt-10 flex items-center gap-x-6">
              <a
                href="#"
                className="rounded-md bg-blue-600 px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-blue-500"
              >
                Get started
              </a>
            </div>
          </div>
        </div>
        <div className="relative lg:col-span-5 lg:-mr-8 xl:col-span-6">
          <img
            className="aspect-[3/2] w-full bg-slate-50 object-cover lg:absolute lg:inset-0 lg:aspect-auto lg:h-full"
            src="/marketing-hero.jpg"
            alt="Product screenshot"
          />
        </div>
      </div>
    </div>
  )
}
```

---

## FEATURE SECTION EXAMPLES

### 3-Column Features

```jsx
const features = [
  {
    name: 'Push to deploy',
    description: 'Morbi viverra dui mi arcu sed. Tellus semper adipiscing.',
    icon: CloudArrowUpIcon,
  },
  {
    name: 'SSL certificates',
    description: 'Sit quis amet rutrum tellus ullamcorper ultricies.',
    icon: LockClosedIcon,
  },
  {
    name: 'Database backups',
    description: 'Quisque est vel vulputate cursus. Risus proin diam nunc.',
    icon: ServerIcon,
  },
]

export function Features3Column() {
  return (
    <div className="bg-white py-24 sm:py-32">
      <div className="mx-auto max-w-7xl px-6 lg:px-8">
        <div className="mx-auto max-w-2xl lg:text-center">
          <h2 className="text-base font-semibold leading-7 text-blue-600">
            Deploy faster
          </h2>
          <p className="mt-2 text-3xl font-bold tracking-tight text-slate-900 sm:text-4xl">
            Everything you need to deploy your app
          </p>
        </div>
        <div className="mx-auto mt-16 max-w-2xl sm:mt-20 lg:mt-24 lg:max-w-none">
          <dl className="grid max-w-xl grid-cols-1 gap-x-8 gap-y-16 lg:max-w-none lg:grid-cols-3">
            {features.map((feature) => (
              <div key={feature.name} className="flex flex-col">
                <dt className="flex items-center gap-x-3 text-base font-semibold leading-7 text-slate-900">
                  <feature.icon className="h-5 w-5 flex-none text-blue-600" />
                  {feature.name}
                </dt>
                <dd className="mt-4 flex flex-auto flex-col text-base leading-7 text-slate-600">
                  <p className="flex-auto">{feature.description}</p>
                </dd>
              </div>
            ))}
          </dl>
        </div>
      </div>
    </div>
  )
}
```

---

## CUSTOMIZATION POINTS

### Colors
Replace Tailwind color classes with your design tokens:
```jsx
// Default
className="bg-blue-600 text-white"

// Customized with tokens
className="bg-primary-600 text-white"
```

### Typography
Adjust heading sizes for your brand:
```jsx
// Default
className="text-4xl sm:text-6xl"

// More conservative
className="text-3xl sm:text-5xl"
```

### Spacing
Modify section padding:
```jsx
// Default
className="py-24 sm:py-32"

// Tighter
className="py-16 sm:py-24"
```

### Images
Replace placeholder images:
```jsx
src="/your-product-screenshot.png"
alt="Descriptive alt text"
```

---

## ACCESSIBILITY NOTES

- All interactive elements have focus states
- Color contrast meets WCAG AA
- Semantic HTML structure (h1, h2, nav, main, section)
- Alt text for all images
- Skip links for navigation

---

## MOBILE RESPONSIVE BEHAVIOR

All templates include:
- Mobile-first base styles
- `sm:` breakpoint (640px) for tablets
- `lg:` breakpoint (1024px) for desktops
- Stacked layouts on mobile
- Appropriate touch targets (44x44px minimum)

---

**Related:** [MARKETING-DOMAIN-guide](../MARKETING-DOMAIN-guide.md) | [10a-SETUP](./10a-SETUP.md)
