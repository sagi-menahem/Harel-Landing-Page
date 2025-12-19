<div align="center">
  <img src="public/logo.png" alt="Logo" width="150"/>
  <h1>Harel Shemesh - Artist Portfolio Website</h1>
  <h3>Commercial Project Showcase</h3>
  <p>A modern, responsive portfolio website showcasing original canvas paintings, handmade terrariums, and upcycled art by Israeli artist Harel Shemesh.</p>

  <a href="https://harelshemesh.co.il">
    <img src="https://img.shields.io/badge/Live%20Demo-harelshemesh.co.il-5c6b50?style=for-the-badge&logo=globe&logoColor=white" alt="Live Demo">
  </a>
  
  <br />
  
  </div>

---

> **ℹ️ Note:** This repository serves as a **technical showcase** for a commercial project. Due to client privacy and intellectual property rights, the source code is closed. However, this README documents the architectural decisions, tech stack, and features implemented in the production build.

---

<div align="center">

### Tech Stack

[![React](https://img.shields.io/badge/React-19.2.0-61DAFB?style=flat-square&logo=react)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.9.3-3178C6?style=flat-square&logo=typescript)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-v4-06B6D4?style=flat-square&logo=tailwindcss)](https://tailwindcss.com/)
[![Vite](https://img.shields.io/badge/Vite-7.2.2-646CFF?style=flat-square&logo=vite)](https://vitejs.dev/)
[![Framer Motion](https://img.shields.io/badge/Framer_Motion-Animation-0055FF?style=flat-square&logo=framer)](https://www.framer.com/motion/)

</div>

---

## ✨ Key Features

This project was built with a focus on performance, accessibility, and visual impact.

- **🎨 Interactive Galleries** - Custom Lightbox implementation supporting touch gestures (swipe), keyboard navigation, and zoom.
- **📱 Mobile-First Design** - Fully responsive layouts optimized for all screen sizes with specific mobile/desktop optimizations.
- **🧱 Smart Masonry Layout** - JavaScript-based column distribution algorithm that handles dynamic image heights while maintaining correct RTL order.
- **♿ Comprehensive Accessibility** - A dedicated A11y menu offering:
    - Grayscale & High Contrast modes
    - Text resizing & Link highlighting
    - Reduced motion preferences
    - Full screen reader support
- **🖱️ Custom UX Elements** - Desktop-only artistic cursor that gracefully falls back to system defaults on touch devices.
- **⚡ High Performance** -
    - Self-hosted fonts (Heebo & Assistant) to eliminate layout shifts.
    - Next-gen image formats (WebP) with automatic fallbacks.
    - Lazy loading strategy for heavy components.

## 🛠️ Architecture & Technical Decisions

### 1. Adoption of Tailwind CSS v4
I chose to implement the latest alpha/beta features of Tailwind v4 to leverage the new engine:
- **Zero-config approach:** Configuration handled via native CSS variables and `@theme` directives in `index.css`.
- **Performance:** utilizing the `@tailwindcss/vite` plugin for instant HMR and smaller bundle sizes.

### 2. Centralized Content Management
To allow for easy updates without touching the codebase, the architecture separates data from UI:
- All textual content, image paths, and gallery data are managed in a strict TypeScript structure (`src/data/content.ts`).
- Components consume this data, making the UI purely presentational and reusable.

### 3. Custom Logic Implementation
Instead of relying on heavy third-party libraries for every feature, I implemented custom hooks and utilities:
- **`useScrollSpy`**: For updating navigation state based on viewport position.
- **Masonry Logic**: A custom algorithm to distribute images into columns based on container width and aspect ratios, specifically solving RTL ordering issues common in standard libraries.

## 🎨 Design System

The design reflects the artist's natural and organic style:

- **Palette:** - 🟢 **Olive** (`#5c6b50`) - Primary accents
  - 🟡 **Gold** (`#d4af37`) - Highlights
  - 🌑 **Dark** (`#2d2d2d`) - Typography
  - ⚪ **Cream** (`#F0EFE9`) - Background

- **Typography:** - *Heebo* (Display) & *Assistant* (Body) - Chosen for excellent Hebrew legibility and modern aesthetic.

---

## 👨‍💻 Developed By

**Sagi Menahem**

I am a final-year Computer Science student (B.Sc.) passionate about building polished, high-performance web experiences.

[![GitHub](https://img.shields.io/badge/GitHub-sagiia-181717?style=flat-square&logo=github)](https://github.com/sagiia)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/sagi-menachem/)
[![Email](https://img.shields.io/badge/Email-Contact_Me-D14836?style=flat-square&logo=gmail&logoColor=white)](mailto:your-email@example.com)

---

<div align="center">
  <small>© 2025 All Rights Reserved to Harel Shemesh (Content) & Sagi Menahem (Code).</small>
</div>
