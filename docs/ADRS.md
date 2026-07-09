# 🏛️ Architecture Decision Records (ADRs)

Este documento registra las decisiones arquitectónicas fundamentales tomadas durante el diseño y desarrollo de **CinloLabs**, detallando el contexto, la decisión adoptada y sus consecuencias.

---

# ADR 001: Adopción de Feature-Driven Architecture (FDA)

## Contexto
En aplicaciones web tradicionales organizadas por capas técnicas (`components/`, `hooks/`, `services/`), agregar una nueva especialidad o módulo de negocio obliga a dispersar código en múltiples carpetas, aumentando el acoplamiento y dificultando el mantenimiento.

## Decisión
Adoptar una arquitectura orientada a características (**Feature-Driven Architecture - FDA**), encapsulando componentes de UI, hooks, servicios, repositorios y tipos dentro de subdirectorios cohesivos en `features/*` (`features/tenant`, `features/portal`, `features/products/dental-lab`, etc.).

## Consecuencias
- **Positivas:** Cohesión máxima del código por dominio de negocio; facilidad de borrado o expansión modular sin afectar al resto.
- **Consideraciones:** Requiere disciplina para no duplicar código genérico (lo cual se resuelve delegando en `features/core`).

---

# ADR 002: Separación Estricta de Dominios con Validación en CI (`check-boundaries`)

## Contexto
En una plataforma SaaS Multi-Tenant que combina el panel de administración global del SaaS (`platform/`) y los dashboards del cliente (`tenant/`), una importación cruzada accidental podría filtrar lógica confidencial o generar dependencias circulares peligrosas.

## Decisión
Establecer fronteras arquitectónicas infranqueables: el código de `tenant` **nunca puede importar** de `platform` ni viceversa. Para asegurar su cumplimiento inmutable, se creó el script automatizado `scripts/check-boundaries.js` que se ejecuta obligatoriamente en el pipeline de Integración Continua (CI).

## Consecuencias
- **Positivas:** Prevención automatizada de fugas arquitectónicas y acoplamientos entre el superadmin y los tenants.
- **Consideraciones:** Todo contrato o componente verdaderamente universal debe ser promovido al dominio neutro `features/core`.

---

# ADR 003: Aislamiento Multi-Tenant con Row Level Security (RLS)

## Contexto
Garantizar que ningún laboratorio dental pueda consultar por error u omisión de código órdenes o pacientes de otro laboratorio es un requisito crítico de seguridad.

## Decisión
Implementar un esquema multi-tenant lógico por columna (`tenant_id`) reforzado en el motor de base de datos con **PostgreSQL Row Level Security (RLS)** en Supabase.

## Consecuencias
- **Positivas:** Seguridad a nivel de kernel de base de datos. Incluso si un query del servidor omitiera el filtro `WHERE tenant_id = ...`, RLS impide el acceso a datos ajenos.
- **Consideraciones:** Todas las consultas del lado del servidor deben estar autenticadas o usar políticas explícitas en Supabase.

---

# ADR 004: Patrón Repositorio Desacoplado y Abstracción de Facturación

## Contexto
La lógica de negocio (`services/`) no debe depender directamente de llamadas de un ORM concreto o de una API externa específica de pago, ya que dificultaría las pruebas unitarias y la migración tecnológica.

## Decisión
Implementar interfaces de adaptadores tanto para persistencia (`Repository Pattern` en `features/*/lib/*.repository.ts`) como para cobros (`Billing Provider Interface`).

## Consecuencias
- **Positivas:** Capacidad de ejecutar el sistema completo en modo local o de prueba (`Mock Mode`) sin base de datos real ni claves de pago en las etapas iniciales de desarrollo.

---

# ADR 005: Prevención de Hydration Mismatches en Next.js (SSR vs CSR)

## Contexto
El renderizado del lado del servidor (SSR) en Next.js puede generar errores de discrepancia (`Hydration Mismatch`) cuando el DOM del servidor no coincide con el cliente (ej. por datos leídos del `localStorage` como carritos o configuraciones locales del tenant).

## Decisión
Hidratar el estado persistido en el cliente estrictamente dentro de bloques `useEffect` una vez montado el componente en el navegador y aislar claves de almacenamiento por marca y entorno (`Dynamic Scoping`).

## Consecuencias
- **Positivas:** Erradicación total de errores visuales de hidratación en producción.
