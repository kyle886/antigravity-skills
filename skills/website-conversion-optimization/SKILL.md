---
name: website-conversion-optimization
description: B2B marketing website conversion patterns — trust signals, CTA hierarchy, social proof, lead capture forms, and above-the-fold optimization. Specific to CRE and professional services websites. Use when a website looks good but doesn't convert visitors into leads.
---

# Website Conversion Optimization

Patterns that turn visitors into leads on B2B marketing websites, specifically for CRE and professional services.

## Use this skill when

- A website looks polished but isn't generating leads
- Optimizing above-the-fold content for first impressions
- Adding social proof and trust signals
- Designing contact forms for maximum completion rate
- Structuring the homepage narrative for conversion

## Do not use this skill when

- Building e-commerce (different conversion patterns)
- Doing A/B testing setup (use analytics tools)
- Working on SEO (use the SEO skills)

## Instructions

### Pattern 1: Above-the-Fold Formula

The first screen must answer 3 questions within 5 seconds:

1. **What do you do?** (headline)
2. **Why should I care?** (subheadline with value prop)
3. **What should I do next?** (primary CTA)

```tsx
function HeroSection() {
  return (
    <section className="min-h-[90dvh] flex items-center">
      <div className="container max-w-3xl">
        {/* Trust indicator — subtle but present */}
        <p className="text-sm text-muted-foreground mb-4">
          Trusted by 150+ Australian businesses
        </p>

        {/* Headline: What you do + who it's for */}
        <h1 className="text-4xl md:text-6xl font-bold tracking-tight">
          Commercial Real Estate Intelligence for{" "}
          <span className="text-primary">Smarter Decisions</span>
        </h1>

        {/* Subheadline: Value prop in 1 sentence */}
        <p className="mt-6 text-lg text-muted-foreground max-w-2xl">
          We combine data analytics, market intelligence, and industry expertise
          to help you find, evaluate, and secure the right commercial space.
        </p>

        {/* CTA pair: Primary + Secondary */}
        <div className="mt-8 flex flex-wrap gap-4">
          <Button size="lg" className="text-base px-8">
            Get Market Insights
          </Button>
          <Button size="lg" variant="outline" className="text-base px-8">
            View Our Markets
          </Button>
        </div>
      </div>
    </section>
  );
}
```

**Rules:**

- Primary CTA is action-oriented: "Get X", "Start Y" — never "Learn More"
- Only ONE primary CTA per screen
- Secondary CTA is lower commitment ("View Portfolio", "See Examples")
- No hamburger nav on desktop — show key navigation items

---

### Pattern 2: Trust Signals Hierarchy

Trust signals build credibility throughout the page. Layer them:

**Tier 1 (Above the fold):**

- Client count: "Trusted by 150+ Australian businesses"
- Industry credential: "SIOR Designated"
- Client logos (4–6 recognizable brands)

**Tier 2 (Mid-page):**

- Specific metrics: "$2.3B in transactions completed"
- Testimonial quotes with real names and companies
- Case study previews with measurable outcomes

**Tier 3 (Bottom of page):**

- Team photos (real people, not stock)
- Office address (physical presence = legitimacy)
- Professional affiliations / awards

```tsx
function TrustBar() {
  return (
    <div className="border-y bg-muted/30 py-8">
      <div className="container">
        <p className="text-center text-sm text-muted-foreground mb-6">
          Trusted by industry leaders
        </p>
        <div className="flex flex-wrap items-center justify-center gap-x-12 gap-y-4">
          {[
            { src: "/logos/cbre.svg", alt: "CBRE", width: 80 },
            { src: "/logos/jll.svg", alt: "JLL", width: 60 },
            /* ... more logos */
          ].map((logo) => (
            <img
              key={logo.alt}
              src={logo.src}
              alt={logo.alt}
              width={logo.width}
              className="h-8 object-contain opacity-60 hover:opacity-100 transition-opacity"
            />
          ))}
        </div>
      </div>
    </div>
  );
}
```

---

### Pattern 3: Stats Section That Converts

Numbers build instant credibility. Three rules:

1. Use exactly 3 or 4 stats (cognitive sweet spot)
2. Make numbers big and bold, labels small and muted
3. Animate counters on scroll-in (see `premium-web-animations` skill)

```tsx
function StatsSection() {
  const stats = [
    { value: "$2.3B", label: "Transactions Completed" },
    { value: "150+", label: "Active Clients" },
    { value: "12", label: "Markets Covered" },
    { value: "98%", label: "Client Satisfaction" },
  ];

  return (
    <section className="py-16 bg-primary text-primary-foreground">
      <div className="container grid grid-cols-2 md:grid-cols-4 gap-8">
        {stats.map((stat) => (
          <div key={stat.label} className="text-center">
            <p className="text-3xl md:text-5xl font-bold">{stat.value}</p>
            <p className="mt-2 text-sm opacity-80">{stat.label}</p>
          </div>
        ))}
      </div>
    </section>
  );
}
```

---

### Pattern 4: Contact Form Best Practices

**Fewer fields = higher completion**. For B2B lead capture:

Must-have: Name, Email, Phone, Message (4 fields)
Nice-to-have: Company, Property type (only if qualifying)

```tsx
function ContactForm() {
  return (
    <form className="space-y-4 max-w-md">
      {/* Combine first + last into one field */}
      <div>
        <Label htmlFor="name">Full Name</Label>
        <Input id="name" placeholder="Jane Smith" required />
      </div>

      <div>
        <Label htmlFor="email">Work Email</Label>
        <Input
          id="email"
          type="email"
          placeholder="jane@company.com"
          required
        />
      </div>

      <div>
        <Label htmlFor="phone">Phone</Label>
        <Input id="phone" type="tel" placeholder="+61 4XX XXX XXX" />
      </div>

      <div>
        <Label htmlFor="message">How can we help?</Label>
        <Textarea
          id="message"
          placeholder="I'm looking for office space in Sydney CBD..."
          rows={4}
        />
      </div>

      <Button type="submit" className="w-full" size="lg">
        Send Enquiry
      </Button>

      {/* Privacy reassurance — reduces friction */}
      <p className="text-xs text-muted-foreground text-center">
        We respect your privacy. No spam, ever.
      </p>
    </form>
  );
}
```

**Rules:**

- Labels above inputs, never placeholder-only
- Submit button full-width on mobile (big tap target)
- Add privacy reassurance below the submit button
- Show success state immediately (optimistic UI)
- Auto-focus first field on desktop

---

### Pattern 5: Page Narrative Structure

A marketing page should tell a story. The proven structure:

```
1. HERO        → What we do + CTA
2. TRUST BAR   → Client logos (proof someone uses this)
3. PAIN POINT  → The problem we solve (empathy)
4. SOLUTION    → How we solve it (3 pillars/features)
5. PROOF       → Case study or testimonial
6. STATS       → Numbers that reinforce credibility
7. VERTICALS   → "We work with..." (segmentation)
8. CTA REPEAT  → Same primary CTA, restated
9. CONTACT     → Form or next step
10. FOOTER     → Navigation, legal, social
```

Each section should be self-contained and scannable — visitors scroll, they don't read linearly.

---

### CRE-Specific Conversion Tips

- **Market data is a lead magnet**: Show teasers of market intelligence to drive enquiries
- **"Request a Market Report" > "Contact Us"**: Specificity increases conversion
- **Show real property images**: Stock photography kills trust in CRE
- **Include team credentials**: SIOR, CCIM, CPM designations matter to CRE clients
- **Location pages**: Each market should have its own landing page for SEO + conversion
- **Response time**: Promise fast responses — "We respond within 4 business hours"
