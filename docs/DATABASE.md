# 🗄 Database Architecture

## Overview

CinloLabs utiliza **PostgreSQL** administrado en la nube a través de **Supabase** como motor central relacional.

El modelo de datos está rigurosamente normalizado y diseñado desde su concepción para soportar multi-tenancy nativo, separando las operaciones de cada laboratorio dental (`tenant`) y garantizando aislamiento mediante **Row Level Security (RLS)**.

---

# 🏗 Diagrama Relacional Principal (Entity Relationship)

```text
       ┌────────────────────────┐
       │        tenants         │
       │------------------------│
       │ PK id (UUID)           │
       │    name                │
       │    slug (Unique)       │
       │    status              │
       │    settings (JSONB)    │
       └───────────┬────────────┘
                   │ 1:N
     ┌─────────────┼─────────────────────────────┐
     ▼             ▼                             ▼
┌──────────────┐  ┌───────────────────────────┐  ┌───────────────────────────┐
│ tenant_users │  │         dentists          │  │         patients          │
│--------------│  │---------------------------│  │---------------------------│
│ PK id        │  │ PK id (UUID)              │  │ PK id (UUID)              │
│ FK tenant_id │  │ FK tenant_id              │  │ FK tenant_id              │
│ FK user_id   │  │    name, clinic, email    │  │    full_name, notes       │
│    role      │  └─────────────┬─────────────┘  └─────────────┬─────────────┘
└──────────────┘                │                              │
                                │ 1:N                          │ 1:N
                                └──────────────┬───────────────┘
                                               ▼
                                 ┌───────────────────────────┐
                                 │          orders           │
                                 │---------------------------│
                                 │ PK id (UUID)              │
                                 │ FK tenant_id              │
                                 │ FK dentist_id             │
                                 │ FK patient_id             │
                                 │    work_type, shade       │
                                 │    status, due_date       │
                                 │    price, created_at      │
                                 └───────────────────────────┘
```

---

# 📋 Tablas Principales

## 1. `tenants` (Laboratorios)
Representa a cada laboratorio de mecánica dental registrado en la plataforma.
- Almacena el identificador único (`id`), el `slug` utilizado para su portal/landing (`/lab/[slug]`) y su configuración de theming (`settings`).

## 2. `dentists` (Odontólogos y Clínicas)
Directorio de odontólogos clientes asociados a un laboratorio particular (`tenant_id`).

## 3. `patients` (Pacientes)
Ficha básica del paciente para el cual se confecciona la prótesis o trabajo dental.

## 4. `orders` (Órdenes de Trabajo Protésico)
Es la entidad central del negocio en CinloLabs. Registra:
- **Tipo de Trabajo (`work_type`):** Prótesis fija, removible, corona de zirconio, ortodoncia, etc.
- **Color / Especificación Clínica (`shade`):** Guía de color protésico e indicaciones técnicas.
- **Estado Dinámico (`status`):** `PENDING`, `IN_PROGRESS`, `TESTING`, `READY`, `DELIVERED`.
- **Fechas y Costos:** Fecha estipulada de entrega (`due_date`) y precio del trabajo.

---

# 🛡️ Políticas de Seguridad a Nivel de Fila (RLS)

Todas las tablas de negocio imponen políticas de seguridad RLS en PostgreSQL, restringiendo el acceso exclusivamente al `tenant_id` correspondiente a la sesión del usuario o al `dentist_id` en el caso del portal del odontólogo.