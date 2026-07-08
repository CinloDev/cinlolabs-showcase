# CinloLabs

SaaS para digitalizar la gestión operativa de laboratorios de mecánica dental — de la carga de un trabajo a su entrega, todo en un solo lugar.

🔗 **Demo en vivo:** [labs.cinlodev.com](https://labs.cinlodev.com)
📌 **Estado:** en producción, con primer cliente en fase de testing. En expansión para incorporar a odontólogos como segundo segmento de usuarios.

---

## El problema

Los laboratorios de mecánica dental suelen gestionar sus trabajos (órdenes, pacientes, entregas) en planillas sueltas o cuadernos, sin trazabilidad ni forma clara de compartir el estado de un trabajo con el odontólogo que lo pidió. CinloLabs centraliza esa operación en un panel único.

## Mi rol

Diseñé y desarrollé la plataforma de punta a punta: producto, arquitectura, frontend y backend.

## Stack técnico

- **Frontend:** Next.js, React, Tailwind CSS
- **Backend / datos:** Supabase (Postgres, Auth, Row Level Security)
- **Arquitectura:** Feature-Driven Architecture (FDA)

## Decisiones de arquitectura

El proyecto sigue una arquitectura modular con **separación estricta de dominios**, validada automáticamente en CI (`check-boundaries`):

- **`platform/`** — todo lo exclusivo del super-admin (analytics, auditoría, gestión de tenants, suscripciones).
- **`tenant/`** — el dashboard de cada laboratorio cliente.
- **`products/`** — módulos verticales de negocio (ej. `dental-lab`), aislados del resto.
- **`core/`** — primitivas, UI compartida "dumb" y utilidades genéricas, sin lógica de negocio.

**Regla de oro, enforced por CI:** código de `tenant` nunca puede importar de `platform` ni viceversa — cada dominio solo se comunica a través de contratos o de `core`. Si alguien rompe esa regla, el pipeline falla.

Dentro de cada feature, la lógica respeta el principio de responsabilidad única en 3 capas (controlador delgado → servicio de negocio → repositorio), lo que permitió migrar la capa de datos de almacenamiento en memoria a Supabase sin tocar API ni UI.

Otras piezas de la arquitectura:
- **RBAC** (control de acceso basado en roles) y **logs de auditoría** sobre acciones críticas.
- **Abstracción del proveedor de facturación** (billing provider interface, con implementación mock intercambiable).
- **Tests de aislamiento multi-tenant** (`tenant-isolation`) para garantizar que un laboratorio nunca vea datos de otro.
- **Row Level Security** en Supabase como segunda capa de aislamiento a nivel de base de datos.
- Decisiones de arquitectura documentadas como **ADRs** (Architecture Decision Records).
- **Prevención de hydration mismatches** en Next.js (SSR vs CSR), hidratando estado del cliente estrictamente dentro de `useEffect`.
- **Landing pages públicas por laboratorio**: cada tenant tiene su propio sitio de presentación (portfolio de trabajos), con contenido 100% personalizable y una capa de theming sobre 4 plantillas visuales disponibles.

## Screenshots

**Portal de odontólogos** — donde el odontólogo carga y envía una orden al laboratorio.
<img width="1263" height="844" alt="Gestion de ordenes desde portal de odontologos" src="https://github.com/user-attachments/assets/0aa1edc0-6e96-46b9-a986-19e33ce71d76" />

**Panel del laboratorio (Admin)** — gestión de órdenes/trabajos en curso.
<img width="1068" height="916" alt="Gestion de ordenes desde el potal de laboratorio" src="https://github.com/user-attachments/assets/8aafb265-628a-4108-b77b-1a5ddd9bff9a" />

**Superadmin** — vista global de tenants/laboratorios (arquitectura multi-tenant).
<img width="1066" height="861" alt="Gestion de Laboratorios" src="https://github.com/user-attachments/assets/c37a77b4-4448-482e-b8a2-65ae20893a2b" />

**Landing pública de un laboratorio** — ejemplo de una de las 4 plantillas disponibles.
<img width="1344" height="905" alt="Plantilla01" src="https://github.com/user-attachments/assets/77f45551-3f2e-4376-962c-0752f6370563" />

---

📫 ¿Preguntas sobre el proyecto? [cinlodev@gmail.com](mailto:cinlodev@gmail.com) · [portfolio.cinlodev.com](https://portfolio.cinlodev.com)

