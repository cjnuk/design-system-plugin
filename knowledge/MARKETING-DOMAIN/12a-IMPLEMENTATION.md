---
title: "12a: Marketing Site Implementation Guide"
version: "2.1"
audience: ["developers", "llm-agents"]
dependencies: ["09a-TEMPLATES", "10a-SETUP", "LAYER-08-ACCESSIBILITY"]
last_updated: "2026-01-18"
---

# 12a: MARKETING SITE IMPLEMENTATION GUIDE

**Purpose:** Complete walkthrough of building a marketing homepage.

---

## IMPLEMENTATION STEPS

### Step 1: Base Layout

```tsx
// src/app/layout.tsx
import './globals.css'
import { Inter } from 'next/font/google'
import { Header } from '@/components/layout/Header'
import { Footer } from '@/components/layout/Footer'

const inter = Inter({ subsets: ['latin'] })

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <Header />
        <main>{children}</main>
        <Footer />
      </body>
    </html>
  )
}
```

### Step 2: Header Navigation

```tsx
// src/components/layout/Header.tsx
'use client'
import { useState } from 'react'
import Link from 'next/link'

const navigation = [
  { name: 'Features', href: '#features' },
  { name: 'Pricing', href: '#pricing' },
  { name: 'About', href: '/about' },
  { name: 'Contact', href: '/contact' },
]

export function Header() {
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false)

  return (
    <header className="fixed inset-x-0 top-0 z-50 bg-white/80 backdrop-blur-sm">
      <nav className="flex items-center justify-between p-6 lg:px-8">
        <div className="flex lg:flex-1">
          <Link href="/" className="-m-1.5 p-1.5">
            <span className="text-xl font-bold text-slate-900">Logo</span>
          </Link>
        </div>
        
        {/* Mobile menu button */}
        <div className="flex lg:hidden">
          <button
            type="button"
            className="-m-2.5 inline-flex items-center justify-center rounded-md p-2.5 text-slate-700"
            onClick={() => setMobileMenuOpen(true)}
            aria-label="Open main menu"
          >
            <svg className="h-6 w-6" fill="none" viewBox="0 0 24 24" strokeWidth="1.5" stroke="currentColor">
              <path strokeLinecap="round" strokeLinejoin="round" d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5" />
            </svg>
          </button>
        </div>
        
        {/* Desktop navigation */}
        <div className="hidden lg:flex lg:gap-x-12">
          {navigation.map((item) => (
            <Link
              key={item.name}
              href={item.href}
              className="text-sm font-semibold leading-6 text-slate-900 hover:text-blue-600"
            >
              {item.name}
            </Link>
          ))}
        </div>
        
        <div className="hidden lg:flex lg:flex-1 lg:justify-end">
          <Link
            href="/login"
            className="text-sm font-semibold leading-6 text-slate-900"
          >
            Log in <span aria-hidden="true">&rarr;</span>
          </Link>
        </div>
      </nav>
    </header>
  )
}
```

### Step 3: Hero Section

```tsx
// src/components/marketing/Hero.tsx
import Link from 'next/link'

export function Hero() {
  return (
    <section className="relative isolate pt-24 pb-16 sm:pt-32 sm:pb-24">
      <div className="mx-auto max-w-7xl px-6 lg:px-8">
        <div className="mx-auto max-w-2xl text-center">
          <h1 className="text-4xl font-bold tracking-tight text-slate-900 sm:text-6xl">
            Build faster with our platform
          </h1>
          <p className="mt-6 text-lg leading-8 text-slate-600">
            Everything you need to launch your product. Start building today 
            and ship to production in minutes.
          </p>
          <div className="mt-10 flex items-center justify-center gap-x-6">
            <Link
              href="/signup"
              className="rounded-md bg-blue-600 px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-blue-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600"
            >
              Get started
            </Link>
            <Link
              href="#features"
              className="text-sm font-semibold leading-6 text-slate-900"
            >
              Learn more <span aria-hidden="true">→</span>
            </Link>
          </div>
        </div>
      </div>
    </section>
  )
}
```

### Step 4: Features Section

```tsx
// src/components/marketing/Features.tsx
import { CloudArrowUpIcon, LockClosedIcon, ServerIcon } from '@heroicons/react/24/outline'

const features = [
  {
    name: 'Push to deploy',
    description: 'Deploy your code with a single command. No configuration needed.',
    icon: CloudArrowUpIcon,
  },
  {
    name: 'SSL certificates',
    description: 'Automatic SSL for all your domains. Security out of the box.',
    icon: LockClosedIcon,
  },
  {
    name: 'Database backups',
    description: 'Automatic daily backups with point-in-time recovery.',
    icon: ServerIcon,
  },
]

export function Features() {
  return (
    <section id="features" className="py-24 sm:py-32">
      <div className="mx-auto max-w-7xl px-6 lg:px-8">
        <div className="mx-auto max-w-2xl lg:text-center">
          <h2 className="text-base font-semibold leading-7 text-blue-600">
            Deploy faster
          </h2>
          <p className="mt-2 text-3xl font-bold tracking-tight text-slate-900 sm:text-4xl">
            Everything you need to deploy your app
          </p>
          <p className="mt-6 text-lg leading-8 text-slate-600">
            Our platform handles the infrastructure so you can focus on building.
          </p>
        </div>
        <div className="mx-auto mt-16 max-w-2xl sm:mt-20 lg:mt-24 lg:max-w-none">
          <dl className="grid max-w-xl grid-cols-1 gap-x-8 gap-y-16 lg:max-w-none lg:grid-cols-3">
            {features.map((feature) => (
              <div key={feature.name} className="flex flex-col">
                <dt className="flex items-center gap-x-3 text-base font-semibold leading-7 text-slate-900">
                  <feature.icon className="h-5 w-5 flex-none text-blue-600" aria-hidden="true" />
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
    </section>
  )
}
```

### Step 5: Assemble Homepage

```tsx
// src/app/page.tsx
import { Hero } from '@/components/marketing/Hero'
import { Features } from '@/components/marketing/Features'
import { Pricing } from '@/components/marketing/Pricing'
import { Testimonials } from '@/components/marketing/Testimonials'
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

## COMMON PITFALLS

### 1. Forgetting Accessibility
```tsx
// ❌ Missing aria-label
<button onClick={handleMenu}>
  <MenuIcon />
</button>

// ✅ With aria-label
<button onClick={handleMenu} aria-label="Open menu">
  <MenuIcon />
</button>
```

### 2. Hardcoded Colors
```tsx
// ❌ Hardcoded
className="bg-[#3b82f6]"

// ✅ Using token classes
className="bg-blue-600"
```

### 3. Missing Focus States
```tsx
// ❌ No focus state
className="rounded-md bg-blue-600 px-3.5 py-2.5"

// ✅ With focus state
className="rounded-md bg-blue-600 px-3.5 py-2.5 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600"
```

---

## PERFORMANCE CHECKLIST

- [ ] All images use Next.js `<Image>` component
- [ ] Above-fold images have `priority` prop
- [ ] Fonts are preloaded via Next.js font optimization
- [ ] No unused JavaScript imported
- [ ] Lighthouse Performance > 90
- [ ] CLS (Cumulative Layout Shift) < 0.1

---

**Related:** [09a-TEMPLATES](./09a-TEMPLATES.md) | [10a-SETUP](./10a-SETUP.md) | [LAYER-08-ACCESSIBILITY](../LAYER-08-ACCESSIBILITY.md)
