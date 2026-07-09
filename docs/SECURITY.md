# 🛡 Security Architecture

La seguridad en CinloLabs se implementa bajo un enfoque de **Defensa en Profundidad (Defense in Depth)**, distribuyendo controles y validaciones en todas las capas del sistema.

---

# 🔐 Flujo de Autenticación

CinloLabs delega la gestión de identidades y autenticación en **Supabase Auth**, respaldada por estándares de seguridad modernos (JWT, cookies seguras HTTP-only y cifrado en tránsito SSL/TLS):

```text
Usuario / Odontólogo
         |
         v
  [ Next.js Middleware ] ─── Verifica JWT / Sesión activa
         |
         v
  [ Supabase Auth ]      ─── Valida credenciales y emite claims de Tenant/Rol
```

---

# 👑 Control de Acceso Basado en Roles (RBAC)

La autorización se divide en roles jerárquicos y granulares según el alcance funcional:

## 1. Roles de Plataforma SaaS (`features/platform`)
- **Superadmin:** Acceso irrestricto al panel de administración general (`platform/`), métricas de observabilidad y gestión global de tenants.
- **Platform Auditor:** Acceso de solo lectura a logs de auditoría y reportes operativos.

## 2. Roles del Laboratorio (`features/tenant`, `features/team`)
- **Tenant Admin:** Propietario o gerente del laboratorio. Administra órdenes, pacientes, odontólogos clientes, plantilla web y configuración del tenant.
- **Técnico / Operador:** Acceso acotado a la visualización y actualización del progreso de las órdenes de trabajo asignadas.

## 3. Rol del Odontólogo (`features/portal`)
- **Odontólogo Cliente:** Acceso estrictamente restringido a su propio portal para crear nuevas órdenes y consultar el estado de sus trabajos clínicos en curso.

---

# 📜 Trazabilidad y Auditoría (`features/audit`)

Las acciones críticas y modificaciones de estado quedan registradas de forma inmutable en el módulo de auditoría (`audit_logs`):
- Creación, modificación o cancelación de órdenes de trabajo.
- Altas y bajas de usuarios o técnicos en un laboratorio.
- Modificaciones en configuraciones del tenant o planes de suscripción.

---

# 🔒 Seguridad de Datos en Base de Datos

- **Aislamiento Multi-Tenant (RLS):** Garantizado a nivel de base de datos de PostgreSQL mediante políticas por `tenant_id`.
- **Protección contra Inyección SQL y XSS:** Sanitización exhaustiva en formularios de entrada y uso de consultas parametrizadas en todo el acceso a datos.