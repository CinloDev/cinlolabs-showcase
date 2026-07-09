# ☁️ Infrastructure

## Overview

CinloLabs opera sobre una infraestructura cloud administrada de alto rendimiento y bajo costo operativo (**Cloud-Native Serverless**), diseñada para escalar automáticamente frente al crecimiento de laboratorios y órdenes de trabajo.

La arquitectura se apoya en tres plataformas principales:
- **Vercel:** Hosting, enrutamiento en Edge y ejecución de Next.js App Router.
- **Supabase:** Base de datos relacional PostgreSQL, Autenticación y Almacenamiento de archivos.
- **Resend:** Despacho seguro de correos electrónicos transaccionales.

---

# 🏗 Diagrama de Infraestructura Cloud

```text
                                Usuario / Odontólogo
                                         │
                                         ▼
                             ┌───────────────────────┐
                             │    Vercel Edge CDN    │
                             └───────────┬───────────┘
                                         │
                    ┌────────────────────┴────────────────────┐
                    ▼                                         ▼
        [ Next.js Edge Middleware ]               [ Next.js Serverless ]
       (Validación JWT / Enrutamiento)             (App Router / Server Actions)
                    │                                         │
                    └────────────────────┬────────────────────┘
                                         │
                    ┌────────────────────┼────────────────────┐
                    ▼                    ▼                    ▼
             ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
             │ Supabase DB  │    │  Supabase    │    │    Resend    │
             │ (PostgreSQL) │    │   Storage    │    │ (Email API)  │
             └──────────────┘    └──────────────┘    └──────────────┘
```

---

# 🌐 Componentes de Infraestructura

## 1. Vercel (Frontend & Compute)
- **Despliegue Continuo:** Despliegues automáticos desde repositorios Git con validación previa en CI (`check-boundaries`).
- **Edge Middleware:** Intercepta cada petición para validar tokens de sesión JWT de Supabase y resolver rutas de laboratorio (`/lab/[slug]`).
- **Serverless Functions:** Ejecuta Server Actions de Next.js con tiempos de latencia mínimos.

## 2. Supabase Cloud (Backend & Persistencia)
- **PostgreSQL Administrado:** Base de datos relacional con respaldos automáticos y protección Row Level Security (RLS).
- **Supabase Auth:** Autenticación en la nube con tokens seguros.
- **Supabase Storage:** Almacenamiento optimizado de imágenes dentales, órdenes adjuntas y recibos PDF.

## 3. Resend (Comunicaciones)
- Despacho transaccional de alta entregabilidad para notificar a odontólogos sobre el avance de sus órdenes y confirmaciones operativas.