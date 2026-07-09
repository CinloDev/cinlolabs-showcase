# 🏢 Multi-Tenancy Architecture

## Overview

CinloLabs opera bajo un modelo **Multi-Tenant SaaS lógico**, donde múltiples laboratorios de mecánica dental coexisten en una infraestructura cloud compartida manteniendo un aislamiento estricto e impenetrable de sus datos operativos, clínicos y comerciales.

La arquitectura garantiza que un laboratorio jamás pueda acceder, visualizar o modificar la información de otro.

---

# 🏗 Modelo de Aislamiento por `tenant_id`

Toda entidad transaccional en la base de datos central de CinloLabs está vinculada explícitamente a un identificador único de laboratorio (`tenant_id`):

```text
                        ┌──────────────────┐
                        │     tenants      │
                        │    (id, slug)    │
                        └────────┬─────────┘
                                 │ 1:N
     ┌───────────────────────────┼───────────────────────────┐
     ▼                           ▼                           ▼
┌───────────────┐         ┌───────────────┐         ┌───────────────┐
│    orders     │         │   patients    │         │   dentists    │
│  (tenant_id)  │         │  (tenant_id)  │         │  (tenant_id)  │
└───────────────┘         └───────────────┘         └───────────────┘
```

---

# 🛡️ Doble Barrera de Seguridad

El aislamiento de datos se aplica mediante dos capas de defensa independientes:

## 1. Capa de Aplicación (Business Services)
Cada solicitud recibida es validada contra la sesión de usuario autenticada (`features/auth`). El servicio inyecta de forma inmutable el `tenant_id` del laboratorio actual en cada consulta y mutación, bloqueando cualquier intento de manipulación en los parámetros de la URL o payload JSON.

## 2. Capa de Base de Datos (Row Level Security - RLS)
Como segunda línea de defensa, PostgreSQL impone políticas de **Row Level Security (RLS)** a nivel de motor. Incluso si un error en la aplicación omitiera un filtro, el motor de base de datos rechaza automáticamente cualquier lectura o escritura cuyas filas no coincidan con el `tenant_id` autorizado para el token JWT activo:

```sql
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Tenant isolation for orders"
ON orders
USING (tenant_id = auth.jwt() ->> 'tenant_id');
```

---

# 🧪 Pruebas Automatizadas de Aislamiento (`tenant-isolation`)

La integridad del modelo multi-tenant se verifica en integración continua mediante suites especializadas de testing (`tenant-isolation`). Estas pruebas simulan peticiones concurrentes de usuarios pertenecientes a distintos laboratorios para verificar que ninguna consulta inter-tenant devuelva resultados.

---

# 🌐 Enrutamiento y Presentación por Tenant

- **Rutas de Laboratorio (`/lab/[tenant-slug]`):** Cada laboratorio dispone de su propio espacio web diferenciado y amigable.
- **Personalización Visual (Theming):** Cada tenant gestiona de forma aislada su paleta cromática, logotipo y plantilla web activa.
- **Evolución a Dominios Personalizados (White-labeling):** Soporte planificado para que laboratorios avanzados vinculen sus propios dominios web (ej. `midentalab.com`) con mapeo transparente en el servidor.
