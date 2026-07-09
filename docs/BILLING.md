# 💳 Billing & Subscriptions Architecture

## Overview

El módulo de facturación de CinloLabs (`features/billing`) gestiona el ciclo de vida de suscripciones SaaS para laboratorios clientes y la posible integración de cobros por servicios en línea.

La arquitectura de facturación está desacoplada del proveedor externo mediante un patrón adaptador (**Billing Provider Interface**).

---

# 🔌 Abstracción del Proveedor de Facturación

Para garantizar alta testeabilidad y flexibilidad operativa, el sistema no interactúa directamente con el SDK del proveedor de pagos en su lógica principal. En su lugar, utiliza un contrato estándar:

```text
               ┌─────────────────────────────────────┐
               │         BillingService              │
               └──────────────────┬──────────────────┘
                                  │
                                  ▼
               ┌─────────────────────────────────────┐
               │      IBillingProvider (Interface)   │
               └──────────┬───────────────┬──────────┘
                          │               │
            ┌─────────────┴─────┐   ┌─────┴──────────────┐
            ▼                   ▼   ▼                    ▼
   [ MockBillingProvider ]          [ MercadoPagoProvider ]
     (Entorno Desarrollo / Test)      (Entorno Producción)
```

## Ventajas de la Abstracción:
- **Desarrollo sin Fricción:** En entornos locales o pruebas automatizadas, el `MockBillingProvider` simula altas, cobros exitosos o fallidos instantáneamente sin requerir credenciales externas.
- **Intercambiabilidad:** Permite operar con **Mercado Pago** en Latinoamérica y añadir fácilmente Stripe en el futuro implementando la misma interfaz.

---

# 🏷️ Planes SaaS para Laboratorios

CinloLabs ofrece suscripciones escalonadas según el volumen operativo del laboratorio:

| Plan | Órdenes Mensuales | Usuarios Técnicos | Portal Odontólogos | Theming Web |
| :--- | :---: | :---: | :---: | :---: |
| **Básico** | Hasta 100 | 2 | Incluido | Plantilla Estándar |
| **Pro** | Ilimitadas | Hasta 10 | Incluido + Archivos | 4 Plantillas Premium |
| **Enterprise** | Ilimitadas | Ilimitados | Personalizado | Dominio propio |

---

# 🔄 Flujo de Gestión de Suscripción (Estado Actual vs. Evolución Futura)

> [!NOTE]
> **Estado Actual (Gestión Manual / Asistida):** Actualmente, la gestión de altas, upgrades de plan y cobros de suscripción se realiza de manera asistida. El pago y la coordinación se gestionan de forma manual (o mediante el envío de enlaces de pago por email), y el Superadmin habilita o actualiza el estado del `tenant` directamente desde el panel de administración (`features/platform`).

### Evolución Planificada (Automatización en Próxima Etapa)
La arquitectura base (`features/billing` y la interfaz `IBillingProvider`) está diseñada para activar el cobro automatizado en una fase posterior del roadmap:
1. El administrador del laboratorio (`Tenant Admin`) seleccionará o cambiará de plan de forma autónoma desde el panel.
2. La transacción se procesará de forma automatizada mediante el proveedor configurado (`MercadoPagoProvider`).
3. Webhooks seguros confirmarán el pago y actualizarán el estado de la suscripción del `tenant` en Supabase en tiempo real.
