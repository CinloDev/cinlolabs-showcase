# ⚙️ Tech Stack

## Overview

CinloLabs está construido sobre un stack tecnológico moderno, robusto y altamente productivo, seleccionado específicamente para ofrecer velocidad extrema, excelente experiencia de usuario e insuperable aislamiento multi-tenant.

---

# 🖥 Core Application Framework

## Next.js 16 (App Router & Turbopack)
- **Marco de desarrollo full-stack principal:** Aprovecha Server Components para rendimiento de renderizado en servidor (SSR) y Server Actions para mutaciones seguras sin necesidad de endpoints API intermedios innecesarios.
- **Turbopack:** Motor de compilación ultrarrápido para ciclos de desarrollo iterativos.

## TypeScript & React 18
- Tipado estricto en el 100% del código base, asegurando contratos robustos entre los módulos funcionales (`features/*`).

---

# 🎨 Estilos y Experiencia Visual

- **Tailwind CSS:** Sistema de diseño de utilidades altamente configurable para crear interfaces coherentes y responsivas.
- **Vanilla CSS:** Utilizado para transiciones fluidas, micro-animaciones y soporte para el motor de **Theming** con variables CSS en tiempo de ejecución.
- **Lucide React:** Colección de iconografía limpia, moderna y liviana.
- **Framer Motion:** Animaciones e interacciones dinámicas en componentes visuales y modales.

---

# 🗄 Backend, Datos & Autenticación

## Supabase (PostgreSQL Cloud)
- **Base de datos relacional:** PostgreSQL de alto rendimiento.
- **Row Level Security (RLS):** Garantía a nivel de kernel de base de datos para el aislamiento de laboratorios (`tenant_id`).
- **Supabase Auth:** Gestión de usuarios, sesiones JWT y cookies seguras.
- **Supabase Storage:** Almacenamiento seguro de archivos adjuntos clínicos, especificaciones de prótesis y comprobantes.

---

# 🔌 Integraciones y Librerías Especializadas

- **Resend & Nodemailer:** Despacho automatizado y confiable de correos transaccionales (notificaciones de cambio de estado a odontólogos, recordatorios y confirmaciones).
- **Mercado Pago:** Integración de pasarela para cobro de suscripciones SaaS y cobros en línea (`features/billing`).
- **jsPDF & html2canvas:** Generación nativa en navegador y servidor de órdenes de trabajo en formato PDF listas para imprimir o enviar.
- **XLSX:** Soporte para exportación de reportes de órdenes y listados de pacientes a planillas de Excel y CSV.
- **Browser Image Compression:** Optimización y compresión automática de fotografías o diagramas dentales antes de cargarse en el almacenamiento en nube.

---

# 🧪 Calidad, Verificación y Testing

- **Script de Límites de Arquitectura (`check-boundaries.js`):** Valida en integración continua que se respete la estricta separación de dominios entre `platform/` y `tenant/`.
- **Playwright:** Suite de pruebas End-to-End (E2E) para verificar flujos críticos operativos e impresión de comprobantes (`print-smoke`).
