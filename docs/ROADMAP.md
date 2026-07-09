# 🗺️ Roadmap

El roadmap de **CinloLabs** orienta la evolución de la plataforma desde un sistema vertical de gestión para laboratorios de mecánica dental hacia un ecosistema digital integral que conecte laboratorios, clínicas y odontólogos.

---

# ✅ Fase 1 — Core SaaS & Multi-Tenancy (Completada)

- [x] Panel centralizado de gestión para laboratorios de mecánica dental (`features/tenant`).
- [x] Gestión de órdenes protésicas (coronas, prótesis, ortodoncia) con trazabilidad de etapas.
- [x] Portal dedicado para odontólogos clientes (`features/portal`) con carga directa de solicitudes y consulta de estados.
- [x] Landing Pages públicas por laboratorio (`/lab/[slug]`) y motor de Theming sobre 4 plantillas visuales.
- [x] Arquitectura modular Feature-Driven (FDA) y script automatizado `check-boundaries` en CI.
- [x] Aislamiento multi-tenant estricto con Row Level Security (RLS) en PostgreSQL (Supabase).

---

# 🚧 Fase 2 — Optimización Comercial & Facturación (En Curso)

- [/] Integración en producción del proveedor de cobros de suscripción (`MercadoPagoProvider` en `features/billing`).
- [/] Panel de analíticas operativas y contables por laboratorio.
- [/] Exportación avanzada de métricas y trabajos en formatos Excel y CSV (`xlsx`).

---

# 🔮 Fase 3 — White-labeling, Soporte Nativo & Seguridad Avanzada (Próximamente)

### 1. Enrutamiento Avanzado y Dominios Personalizados (White-labeling)
- **URLs Amigables sin Prefijo:** Intercepción en Middleware de Next.js para omitir `/lab/` en subdominios limpios.
- **Dominios Propios:** Soporte para que los laboratorios conecten su propio dominio web (ej. `carizortholab.com`) mediante resolución por header `Host` y CNAME.

### 2. Módulo de Soporte Integrado (Admin a Superadmin)
- **Chat en Tiempo Real:** Canal de comunicación 100% nativo dentro del dashboard entre el administrador del laboratorio y el superadmin para resolución ágil de consultas sin depender de emails externos o herramientas de terceros.

### 3. Resguardo de Datos Individual y Papelera de Reciclaje (Soft Delete)
- **Borrado Lógico (`deleted_at`):** Implementación transversal en tablas críticas (`orders`, `patients`, `dentists`) para permitir la restauración instantánea ante eliminaciones accidentales de un operador del laboratorio.

### 4. Expansión del Segmento Odontológico
- Incorporación de módulos clínicos y de agenda para odontólogos como segundo segmento principal de usuarios interconectado con la red de laboratorios.