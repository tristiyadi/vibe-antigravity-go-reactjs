---
description: Create a modern, responsive, and feature-rich landing page for Laundry using React, Vite, Tailwind CSS, and Shadcn UI, including assets and PWA setup.
---

# 02 Frontend Landing Page (Laundry)

This workflow sets up a high-performance, PWA-ready landing page with a premium Blue/White theme, custom assets (logo, hero image), Floating WhatsApp, and comprehensive business sections.

## 1. Initialize Vite Project
// turbo
Initialize the React project with Vite and TypeScript.
```bash
npm create vite@latest frontend -- --template react-ts
```

## 1.1 Setup .env & .gitignore
// turbo
Configure environment variables and update `.gitignore` to prevent secret leaks.
```bash
cd frontend

# Create .env files
cat <<EOF > .env
VITE_API_URL=http://localhost:8080/api
EOF

cat <<EOF > .env.example
VITE_API_URL=http://localhost:8080/api
EOF

# Update .gitignore
cat <<EOF >> .gitignore

# Environment variables
.env
.env.*
!.env.example

# Custom exclusions
build/
.DS_Store
EOF
```

## 2. Install Dependencies
// turbo
Install Tailwind v3 (stable), Framer Motion (for animations), and other required packages.
```bash
cd frontend
npm install
npm install --legacy-peer-deps framer-motion lucide-react react-helmet-async clsx tailwind-merge vite-plugin-pwa react-router-dom @types/node
npm install -D tailwindcss@^3.4 postcss autoprefixer
npx tailwindcss init -p
```

## 3. Setup Assets (Logo & Images)
// turbo
Create necessary directories and place dummy assets. (Replace with actual high-quality assets during development).
```bash
mkdir -p frontend/src/assets frontend/public
# Place logo.png and hero.png in frontend/src/assets/
# Place favicon.png and PWA icons in frontend/public/
```

## 4. Configure Tailwind & Theme
### 4.1 Tailwind Config
Update `frontend/tailwind.config.js`.
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          DEFAULT: "#0ea5e9", // Sky Blue 500
          foreground: "#ffffff",
        },
        secondary: {
          DEFAULT: "#f0f9ff", // Sky Blue 50
          foreground: "#0284c7",
        },
        background: "#ffffff",
        foreground: "#0f172a", // Slate 900
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
      animation: {
        'float': 'float 3s ease-in-out infinite',
      },
      keyframes: {
        float: {
          '0%, 100%': { transform: 'translateY(0)' },
          '50%': { transform: 'translateY(-10px)' },
        }
      }
    },
  },
  plugins: [],
}
```

### 4.2 Global CSS
Update `frontend/src/index.css`.
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  * {
    border-color: hsl(var(--border));
  }

  body {
    @apply bg-background text-foreground antialiased;
  }
}
```

## 5. Implement Components

### 5.1 Navbar
Create `frontend/src/components/Navbar.tsx` using the custom logo.
```tsx
import { motion } from 'framer-motion';
import { Menu, X } from 'lucide-react';
import { useState } from 'react';
import logo from '../assets/logo.png';

export const Navbar = () => {
  const [isOpen, setIsOpen] = useState(false);
  const menuItems = ['Beranda', 'Layanan', 'Harga', 'Tentang', 'Kontak'];

  return (
    <nav className="fixed top-0 left-0 right-0 bg-white/90 backdrop-blur-md shadow-sm z-40">
      <div className="container mx-auto px-4">
        <div className="flex justify-between items-center h-16">
          <motion.div initial={{ opacity: 0, x: -20 }} animate={{ opacity: 1, x: 0 }} className="flex items-center space-x-2">
            <img src={logo} alt="Laundry" className="h-10 w-10 object-contain" />
            <span className="text-2xl font-bold text-primary">Laundry</span>
          </motion.div>
          {/* Menu items... */}
        </div>
      </div>
    </nav>
  );
};
```

### 5.2 Hero Section
Create `frontend/src/components/Hero.tsx` using the premium hero image.
```tsx
import { motion } from 'framer-motion';
import heroImg from '../assets/hero.png';

export const Hero = () => {
  return (
    <section id="beranda" className="relative min-h-screen flex items-center justify-center pt-16 overflow-hidden">
        {/* Background Image with Overlay */}
        <div className="absolute inset-0 z-0">
            <img src={heroImg} alt="Laundry background" className="w-full h-full object-cover opacity-10" />
            <div className="absolute inset-0 bg-gradient-to-br from-blue-50/80 via-white/40 to-blue-100/80" />
        </div>
        
        <div className="container mx-auto px-4 text-center z-10">
            {/* Content... */}
        </div>
    </section>
  );
};
```

## 6. PWA Setup
Update `frontend/vite.config.ts`. (Ensure icons match the paths in step 3).

## 7. Main App & SEO
Update `frontend/src/App.tsx`.
```tsx
<Helmet>
    <title>Laundry | Jasa Laundry Premium Bogor</title>
    <link rel="icon" type="image/png" href="/favicon.png" />
    {/* Other meta tags... */}
</Helmet>
```

## 8. Development
// turbo
```bash
cd frontend
npm run dev
```