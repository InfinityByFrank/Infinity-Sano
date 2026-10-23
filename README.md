# 🥗 Sano — Food scanning & scoring: mobile app, desktop dashboard, API and landing

🌐 **English** · [Español](README_es.md)

A complete product that turns a product barcode into a **0–100 health score** with Mexican **NOM-051** warning labels, explained additives and healthier alternatives. I designed and built every piece: the **mobile app**, the **desktop operations dashboard**, the **NestJS gateway**, a **local AI assistant** that reads packaging photos, and the **launch landing page**.

<p align="center">
  <img src="assets/movil-05-producto-detalle.png" width="230" alt="Product detail with score, Nutri-Score, NOVA and warning labels" />
  <img src="assets/dashboard-productos.png" width="620" alt="Dashboard product catalog (9,207 products)" />
</p>

---

## 🎯 About the project

Mexico has one of the strictest food-labelling systems in the world, but the black warning seals only say *what is wrong*, not *how wrong*, nor *what to choose instead*. Two products can carry the same sugar seal with very different amounts, and the seals don't consider the person (diabetes, hypertension, shopping for kids).

**Sano turns information that already exists into a simple decision at the moment it matters.**

| Area | What I did |
|---|---|
| Product | Defined value proposition, differentiators and scope |
| Mobile app | Expo + TypeScript: scanning, score, profile |
| Dashboard | Electron + React: catalog, users, score, reports, support |
| Backend | NestJS gateway with Docker Compose |
| AI | Data extraction from package labels with a local vision model |
| Web | Astro landing page |
| Quality | Per-module docs, versioned `TODO` / `PENDIENTES` / `HECHO` files |

## 🛠️ Tech stack

`React Native / Expo` · `Electron` · `React` · `TypeScript` · `Tailwind CSS v4` · `Zustand` · `NestJS` · `Docker` · `Astro` · `ONNX Runtime` · `llama.cpp (Qwen2.5-VL)` · `Cloudflare R2`

## 📱 Mobile app

Onboarding → home → product detail with score, Nutri-Score, NOVA, NOM-051 seals, ingredients and healthier alternatives. Built with Expo (dynamic `app.config.ts`, EAS builds, a publishing guide).

<p align="center">
  <img src="assets/movil-01-onboarding.png" width="190" alt="Onboarding" />
  <img src="assets/movil-04-home.png" width="190" alt="Home: top 5 sodas" />
  <img src="assets/movil-06-producto-nutrimental.png" width="190" alt="Ingredients" />
  <img src="assets/movil-07-producto-aditivos.png" width="190" alt="Alternatives and profile" />
</p>

> The scanner screen needs a physical iPhone (the simulator has no camera), so it isn't in these screenshots.

## 🖥️ Desktop dashboard

An operations panel for the team: KPIs with range filters and a daily trend, a **9,207-product catalog** with search, category filter, completeness and image-review status, a score-health module, and a moderation queue for reported content.

<p align="center">
  <img src="assets/dashboard-inicio.png" width="460" alt="Dashboard home with KPIs" />
  <img src="assets/dashboard-catalogo.png" width="460" alt="Catalog health and composition" />
</p>
<p align="center">
  <img src="assets/dashboard-score.png" width="460" alt="Score health module" />
  <img src="assets/dashboard-calidad-datos.png" width="460" alt="Data gaps and NOM-051 seals charts" />
</p>
<p align="center">
  <img src="assets/dashboard-reportes.png" width="460" alt="Content moderation queue" />
</p>

> The numbers shown on the home dashboard (11 scans, 1 user) are **test data**, not results.

## 🌐 Landing page

Astro + React + Tailwind v4, deployable on Cloudflare (`wrangler`): hero with a score card, interactive comparator and weekly report.

<p align="center"><img src="assets/landing-hero.png" width="640" alt="Sano landing hero" /></p>

## 🧠 Key technical decisions

### Secure desktop architecture (Electron)
- Strict `main` / `preload` / `renderer` separation.
- **JWTs never reach React**; the refresh token is encrypted with `safeStorage` (macOS Keychain).
- Allow-list of hosts for API calls and external links; the origin of every IPC call is validated.

### Local AI, decided with a proof of concept
Before implementing, I ran a POC: a 2B model on CPU reads seals and brand fine but **fails the nutrition table**; a 7B reads it in full. Decision: run **Qwen2.5-VL-7B** as a subprocess of the main process through `llama-server` (Metal/GPU), without loosening the CSP or opening a second window.

- Output forced by **JSON schema**, images analysed one by one in a queue.
- **EAN-13 check-digit validation** on the barcode.
- The assistant takes three photos (front, side, back) and **pre-fills the product form**; the team reviews and confirms.

### Image pipeline
Presigned uploads to **Cloudflare R2**, a crop editor and **background removal with a local ONNX model**.

### Modular structure for a dashboard that grows
`modules/<domain>/{pages,components,services,store,types}`, a Zustand store per module, and `shared` only for cross-process contracts.

### Backend & deploy
NestJS gateway with `docker-compose`; HTTPS and allowed hosts are mandatory environment variables in production.

## 🌱 Next steps (honest list)

- Bundle `llama-server` and the GGUF models with electron-builder.
- Per-image crop to frame the nutrition table.
- Dedicated barcode reader (zxing) as a fallback to the model.
- Automated tests for the vision module.
- Windows (DPAPI) and Linux support are out of scope for now.

## 📄 Note

This repository contains **documentation only**; the source code is private. Happy to walk through it in an interview.

## 📬 Contact

**Infinity dev by Frank** · [GitHub](https://github.com/InfinityByFrank) · [Infinity](https://infinitydev.agency) · [Portfolio](https://portfolio.infinitydev.agency)
