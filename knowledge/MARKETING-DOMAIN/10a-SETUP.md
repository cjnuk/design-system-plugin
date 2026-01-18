---
title: "10a: Marketing Site Setup & Build"
version: "2.1"
audience: ["developers", "llm-agents"]
dependencies: ["LAYER-04-CSS-ARCHITECTURE"]
last_updated: "2026-01-18"
---

# 10a: MARKETING SITE SETUP & BUILD

**Purpose:** Step-by-step setup guide for marketing sites using Tailwind UI.

---

## QUICK START

```bash
# 1. Create Next.js project
npx create-next-app@latest marketing-site --typescript --tailwind --app

# 2. Navigate to project
cd marketing-site

# 3. Install additional dependencies
npm install @headlessui/react @heroicons/react

# 4. Copy Tailwind UI templates (from your license)
# Download from https://tailwindui.com/

# 5. Start development
npm run dev
```

---

## PROJECT STRUCTURE

```
marketing-site/
├── src/
│   ├── app/
│   │   ├── layout.tsx          # Root layout
│   │   ├── page.tsx            # Homepage
│   │   ├── about/page.tsx      # About page
│   │   ├── pricing/page.tsx    # Pricing page
│   │   └── contact/page.tsx    # Contact page
│   ├── components/
│   │   ├── marketing/          # Tailwind UI templates
│   │   │   ├── Hero.tsx
│   │   │   ├── Features.tsx
│   │   │   ├── Pricing.tsx
│   │   │   ├── Testimonials.tsx
│   │   │   ├── FAQ.tsx
│   │   │   ├── CTA.tsx
│   │   │   └── Newsletter.tsx
│   │   ├── layout/
│   │   │   ├── Header.tsx
│   │   │   └── Footer.tsx
│   │   └── ui/                 # Shared UI components
│   │       └── Button.tsx
│   └── styles/
│       └── globals.css
├── public/
│   └── images/                 # Marketing images
├── tailwind.config.ts
└── package.json
```

---

## TAILWIND CONFIGURATION

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss'

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        // Map to design tokens from LAYER-01
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
        },
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}

export default config
```

---

## COPYING TAILWIND UI TEMPLATES

### Step 1: Download from Tailwind UI
1. Log in to https://tailwindui.com/
2. Browse Marketing > Hero Sections
3. Copy the React/JSX code

### Step 2: Create Component File
```tsx
// src/components/marketing/Hero.tsx
'use client'

export function Hero() {
  return (
    // Paste Tailwind UI code here
    <div className="relative isolate px-6 pt-14 lg:px-8">
      {/* ... */}
    </div>
  )
}
```

### Step 3: Customize
1. Replace placeholder text
2. Update colors to match tokens
3. Swap placeholder images
4. Adjust spacing if needed

---

## PAGE COMPOSITION

```tsx
// src/app/page.tsx
import { Hero } from '@/components/marketing/Hero'
import { Features } from '@/components/marketing/Features'
import { Testimonials } from '@/components/marketing/Testimonials'
import { Pricing } from '@/components/marketing/Pricing'
import { FAQ } from '@/components/marketing/FAQ'
import { CTA } from '@/components/marketing/CTA'

export default function HomePage() {
  return (
    <>
      <Hero />
      <Features />
      <Testimonials />
      <Pricing />
      <FAQ />
      <CTA />
    </>
  )
}
```

---

## IMAGE OPTIMIZATION

```tsx
// Use Next.js Image component
import Image from 'next/image'

<Image
  src="/hero-image.jpg"
  alt="Product screenshot"
  width={1200}
  height={800}
  priority  // For above-fold images
  className="rounded-lg shadow-xl"
/>
```

---

## SEO SETUP

```tsx
// src/app/layout.tsx
import type { Metadata } from 'next'

export const metadata: Metadata = {
  title: 'Your Product | Marketing Site',
  description: 'Your product description for SEO',
  openGraph: {
    title: 'Your Product',
    description: 'Your product description',
    images: ['/og-image.jpg'],
  },
}
```

---

## BUILD & DEPLOY

```bash
# Build for production
npm run build

# Preview production build
npm run start

# Deploy to Vercel
vercel deploy
```

### Performance Checklist

- [ ] Images optimized (WebP, correct sizes)
- [ ] Fonts preloaded
- [ ] CSS purged (automatic with Tailwind)
- [ ] Lighthouse score > 90
- [ ] Core Web Vitals passing

---

**Related:** [09a-TEMPLATES](./09a-TEMPLATES.md) | [12a-IMPLEMENTATION](./12a-IMPLEMENTATION.md)
