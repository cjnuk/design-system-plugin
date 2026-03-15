# MARKETING DOMAIN: COMPLETE GUIDE

## File Structure

### MARKETING-DOMAIN/09a-TEMPLATES.md
**Purpose:** Document all available templates and customization points

Key sections:
- Hero section variants (5+)
- Feature sections (2-column, 3-column, stacked)
- Pricing tables
- Testimonials components
- FAQ sections
- CTA templates
- Navigation components
- Footer components
- Customization points for each template

---

### MARKETING-DOMAIN/10a-SETUP.md
**Purpose:** Step-by-step setup guide

Contents:
- Installing Tailwind UI components into project
- File organization (components/marketing/)
- Copying templates while maintaining structure
- Customizing colors using design tokens
- Customizing text and content without breaking layout
- Responsive behavior expectations
- Lighthouse/performance optimization

---

### MARKETING-DOMAIN/12a-IMPLEMENTATION.md
**Purpose:** Complete walkthrough of building a homepage

Step-by-step:
1. Start with base layout
2. Add hero section with customization
3. Add features section with cards
4. Add pricing section with variants
5. Add testimonials carousel
6. Add FAQ accordion
7. Add CTA section
8. Build footer
9. Add SEO meta tags
10. Integrate dark mode
11. Test responsive on mobile/tablet
12. Performance checklist

Each section includes:
- Code example (copy-paste ready)
- Customization options
- Accessibility notes
- Mobile-responsive behavior
- Common pitfalls to avoid

---

## QUICK START: MARKETING SITE

```bash
# 1. Install dependencies
npm install

# 2. Copy Tailwind UI templates
# (Download from https://tailwindui.com/)

# 3. Import components
import { HeroSection } from '@/components/marketing/hero'
import { FeaturesSection } from '@/components/marketing/features'

# 4. Build your page
export default function Home() {
  return (
    <>
      <HeroSection />
      <FeaturesSection />
      <PricingSection />
      <TestimonialsSection />
      <FAQSection />
      <CTASection />
      <Footer />
    </>
  )
}
```

---

## CUSTOMIZATION PATTERN

All templates follow this customization flow:

1. **Color** → Use design token variables
2. **Text** → Edit content props
3. **Images** → Replace image sources
4. **Spacing** → Adjust margin/padding classes
5. **Responsiveness** → Already handled via Tailwind
6. **Animations** → Optional, use Tailwind transforms

Example:

```jsx
// Before customization
<HeroSection />

// After customization
<HeroSection
  title="My Product"
  subtitle="Amazing features"
  cta="Get Started"
  image="/my-image.jpg"
  isDark={true}
/>
```

---

## PERFORMANCE CHECKLIST

- [ ] Images optimized (WebP, right size)
- [ ] CSS purged (unused classes removed)
- [ ] Fonts limited to 2-3 weights
- [ ] No unused dependencies
- [ ] Lighthouse score > 90
- [ ] Interaction to Paint < 100ms
- [ ] First Contentful Paint < 2.5s

---

## DEPLOYMENT

1. Build static site: `npm run build`
2. Deploy to Vercel/Netlify
3. Set up analytics
4. Configure SEO meta tags
5. Enable gzip compression
6. Set cache headers

Files include complete Next.js config example.

