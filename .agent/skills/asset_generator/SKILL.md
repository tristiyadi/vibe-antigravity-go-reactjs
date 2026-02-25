---
name: asset_generator
description: Skill for generating premium visual assets (logos, hero images, favicons) using AI generation tools and implementing them in a React/Vite frontend.
---

# Asset Generation & Implementation Skill

This skill outlines the standard process for creating high-quality, premium visual identities for web applications using AI generation and integrating them into a "Clean Blue/White" design system.

## 1. Visual Style Guidelines (Google Nano Banana)
The "Google Nano Banana" aesthetic refers to a design language that is:
- **Clean & Minimalist**: No clutter, plenty of white space.
- **Vibrant yet Professional**: Using high-contrast but harmonious colors (e.g., Sky Blue, Navy, Pure White).
- **Soft Geometry**: Rounded corners, smooth gradients, and subtle shadows.
- **Premium Atmosphere**: High-quality photography style for hero elements.

## 2. Generating Assets

### A. Logo Generation
Always aim for a minimalist vector-style logo that looks good in small sizes (favicon) and large sizes (navbar).

**Prompt Template:**
> "A premium, minimalist modern logo for [Brand Name]. Clean vector design, [Primary Color] and white theme, incorporating a [Industry Symbol] symbol. Professional, balanced, flat design suitable for favicon and branding. Google Nano Banana aesthetic style."

### B. Hero & Background Images
Hero images should feel high-end and set the mood of the service.

**Prompt Template:**
> "A high-quality, professional 4k photograph of [Service Environment/Product]. Neatly arranged, [Theme Colors] lighting, clean atmosphere, premium feel, shallow depth of field. High-end lifestyle photography style."

### C. Dummy Service & Feature Images
Use these for card icons or feature sections to give the web a "complete" look.

**Prompt Templates:**
- **Washing Service**: "A minimalist, high-quality close-up of a modern white washing machine drum with water droplets, soft blue studio lighting, clean and fresh aesthetic."
- **Ironing/Folding**: "Neatly stacked and folded white premium cotton towels and shirts, clean bright background, minimalist professional laundry service style."
- **Delivery/Antar Jemput**: "A clean, minimalist 3D illustration or high-quality photo of a white delivery van with blue accents, professional logistics aesthetic."
- **Quality Shield**: "A premium 3D gold or blue metallic shield icon, minimalist design, soft shadows, clean white background."

## 3. Implementation Workflow

### Step 1: Directory Setup
Ensure the frontend has the correct structure for assets:
```bash
mkdir -p frontend/src/assets frontend/public
```

### Step 2: Saving Assets
- **Logo**: Save as `frontend/src/assets/logo.png`.
- **Hero Image**: Save as `frontend/src/assets/hero.png`.
- **Favicon**: Copy logo to `frontend/public/favicon.png`.
- **PWA Icons**: Copy logo to `frontend/public/pwa-192x192.png` and `frontend/public/pwa-512x512.png`.

### Step 3: Integration in Components

**Navbar Logo:**
```tsx
import logo from '../assets/logo.png';

// ...
<img src={logo} alt="Logo" className="h-10 w-10 object-contain" />
```

**Hero Background:**
```tsx
import heroImg from '../assets/hero.png';

// ...
<div className="absolute inset-0 z-0">
  <img src={heroImg} className="w-full h-full object-cover opacity-20" />
</div>
```

**SEO & Favicon (App.tsx):**
```tsx
<Helmet>
  <link rel="icon" type="image/png" href="/favicon.png" />
</Helmet>
```

## 4. Optimization Tips
- **Opacity**: When using hero images as backgrounds, keep opacity low (10-20%) to ensure text readability.
- **Overlays**: Use color overlays (e.g., `bg-blue-50/80`) on top of hero images to blend them with the theme.
- **Rounding**: Apply `rounded-lg` or `rounded-full` to logos to fit the modern soft-geometry style.
