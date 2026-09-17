# Proyecto VíaMaestra — Base de datos multi-motor

Documentación de la creación de la base de datos del proyecto **VíaMaestra** (formación de conductores) implementada en cuatro motores distintos: **MySQL 8.0**, **PostgreSQL**, **Microsoft SQL Server** y **Oracle**, usando **DBeaver** como cliente de conexión.

Cada sección corresponde a **una tabla (o grupo de tablas relacionadas)** y dentro de esa sección se muestra la evidencia de esa misma tabla ya creada en los 4 motores, una junto a otra.

> Reemplaza cada `![descripción](imagenes/archivo.png)` por tu captura real de DBeaver (clic derecho sobre la tabla → *Ver datos* / *Ver estructura*, o el resultado del `CREATE TABLE` ejecutado sin errores).

---

## 1. Introducción

- **Nombre del proyecto:** VíaMaestra — Academia / agenda / evaluaciones / certificación de conductores.
- **Motores utilizados:** MySQL 8.0, PostgreSQL, Microsoft SQL Server, Oracle Database.
- **Herramienta cliente:** DBeaver (una conexión por motor).
- **Objetivo:** modelar el mismo esquema (módulo de autenticación RBAC + módulo de negocio) en los 4 motores, evidenciando las diferencias de sintaxis entre ellos (autoincremento, tipos ENUM, tipos de texto, etc.).

## 2. Conexiones creadas en DBeaver

Captura de las 4 conexiones activas en el panel de *Database Navigator* de DBeaver.

![Conexiones DBeaver](imagenes/00-conexiones-dbeaver.png)

| Motor | Base de datos | Puerto por defecto |
|---|---|---|
| MySQL | `viamaestra_mysql` | 3306 |
| PostgreSQL | `viamaestra_pg` | 5432 |
| SQL Server | `viamaestra_mssql` | 1433 |
| Oracle | esquema propio (ej. XEPDB1) | 1521 |

## 3. Creación de las bases de datos

Evidencia de la ejecución del `CREATE DATABASE` (o creación del esquema, en el caso de Oracle) en cada motor.

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![MySQL create database](imagenes/03-mysql-create-database.png) | ![PostgreSQL create database](imagenes/03-postgres-create-database.png) | ![SQL Server create database](imagenes/03-sqlserver-create-database.png) | ![Oracle esquema](imagenes/03-oracle-esquema.png) |

---

## 4. Módulo de autenticación y autorización (RBAC)

Modelo transversal obligatorio, común a los 4 motores. Está formado por seis tablas: `users`, `roles`, `role_users` (relación N:M entre usuarios y roles), `resources` (rutas HTTP protegidas, identificadas por `path` + `method`), `resource_roles` (relación N:M entre recursos y roles, es decir, qué rol puede acceder a qué ruta) y `refresh_tokens` (tokens de sesión por dispositivo). En lugar de una captura por tabla, se muestra una sola evidencia por motor con las 6 tablas ya creadas y su estructura, tomada expandiendo el esquema en el árbol de DBeaver (o del resultado de `\dt` / `SELECT * FROM information_schema.tables` según el motor).

### 4.1 MySQL

**Qué se hizo:** se ejecutó el bloque de `CREATE TABLE` del módulo RBAC del script `01_mysql.sql` sobre la base `viamaestra_mysql`, en la pestaña SQL de DBeaver conectada a esa base, usando "Execute SQL Script".
**Cómo se evidencia:** se expandió `viamaestra_mysql → Tables` en el árbol de la conexión para mostrar las 6 tablas creadas en un solo pantallazo.

![RBAC MySQL](imagenes/rbac-mysql.png)

### 4.2 PostgreSQL

**Qué se hizo:** se creó primero el tipo `status_enum` y luego las 6 tablas del script `02_postgresql.sql` sobre la base `viamaestra_pg`.
**Cómo se evidencia:** se expandió `viamaestra_pg → Schemas → public → Tables` en el árbol de DBeaver, mostrando las 6 tablas en una sola captura.

![RBAC PostgreSQL](imagenes/rbac-postgres.png)

### 4.3 SQL Server

**Qué se hizo:** se ejecutó el script `03_sqlserver.sql` (separado por `GO`) sobre la base `viamaestra_mssql` recién creada.
**Cómo se evidencia:** se expandió `viamaestra_mssql → Tables` en el árbol de DBeaver, con las 6 tablas visibles en una sola captura.

![RBAC SQL Server](imagenes/rbac-sqlserver.png)

### 4.4 Oracle

**Qué se hizo:** se ejecutó el bloque RBAC del script `04_oracle.sql` conectado directamente al esquema/usuario de Oracle (sin `CREATE DATABASE`, ya que en Oracle el esquema cumple ese rol).
**Cómo se evidencia:** se expandió el esquema en el árbol de DBeaver (`Tables`), mostrando las 6 tablas creadas en una sola captura.

![RBAC Oracle](imagenes/rbac-oracle.png)

---

## 5. Módulo de negocio — VíaMaestra

### 5.1 Tabla `alumno`

| | |
|---|---|
| **Propósito** | Entidad de negocio que representa al estudiante inscrito en la academia. |
| **Campos clave** | `nombre`, `descripcion`, `is_active`, `created_at`, `updated_at`. |
| **Relaciones** | `user_id UNIQUE` (opcional) → `users.id`, para los alumnos que además cuentan con credenciales de acceso al sistema. |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![alumno MySQL](imagenes/alumno-mysql.png) | ![alumno PostgreSQL](imagenes/alumno-postgres.png) | ![alumno SQL Server](imagenes/alumno-sqlserver.png) | ![alumno Oracle](imagenes/alumno-oracle.png) |

### 5.2 Tabla `curso`

| | |
|---|---|
| **Propósito** | Entidad base que representa la oferta académica (cursos teóricos y prácticos) de VíaMaestra. |
| **Campos clave** | `nombre`, `descripcion`, `is_active`, `created_at`, `updated_at`. |
| **Relaciones** | Ninguna dependencia hacia otras tablas de negocio; es la primera entidad creada en este bloque. |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![curso MySQL](imagenes/curso-mysql.png) | ![curso PostgreSQL](imagenes/curso-postgres.png) | ![curso SQL Server](imagenes/curso-sqlserver.png) | ![curso Oracle](imagenes/curso-oracle.png) |

### 5.3 Tabla `instructor`

| | |
|---|---|
| **Propósito** | Entidad de negocio que representa al instructor a cargo de las sesiones prácticas. |
| **Campos clave** | `nombre`, `descripcion`, `is_active`, `created_at`, `updated_at`. |
| **Relaciones** | `user_id UNIQUE` (opcional) → `users.id`, para instructores que además cuentan con credenciales de acceso al sistema. |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![instructor MySQL](imagenes/instructor-mysql.png) | ![instructor PostgreSQL](imagenes/instructor-postgres.png) | ![instructor SQL Server](imagenes/instructor-sqlserver.png) | ![instructor Oracle](imagenes/instructor-oracle.png) |

### 5.4 Tabla `vehiculo`

| | |
|---|---|
| **Propósito** | Entidad independiente que representa el parque automotor disponible para las sesiones de práctica. |
| **Campos clave** | `nombre`, `descripcion`, `is_active`, `created_at`, `updated_at`. |
| **Relaciones** | Ninguna dependencia directa; es referenciada de forma opcional por `sesion`. |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![vehiculo MySQL](imagenes/vehiculo-mysql.png) | ![vehiculo PostgreSQL](imagenes/vehiculo-postgres.png) | ![vehiculo SQL Server](imagenes/vehiculo-sqlserver.png) | ![vehiculo Oracle](imagenes/vehiculo-oracle.png) |

### 5.5 Tabla `matricula`

| | |
|---|---|
| **Propósito** | Entidad asociativa que materializa la inscripción de un alumno a un curso, con su propio ciclo de vida. |
| **Campos clave** | `fecha`, `periodo`, `estado`. |
| **Relaciones** | `estudiante_id` → `alumno.id`; `oferta_id` → `curso.id`. Resuelve la relación N:M entre `alumno` y `curso`. |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![matricula MySQL](imagenes/matricula-mysql.png) | ![matricula PostgreSQL](imagenes/matricula-postgres.png) | ![matricula SQL Server](imagenes/matricula-sqlserver.png) | ![matricula Oracle](imagenes/matricula-oracle.png) |

### 5.6 Tabla `sesion`

| | |
|---|---|
| **Propósito** | Agenda las clases teóricas y prácticas, evitando solapamientos entre instructores, vehículos y horarios. |
| **Campos clave** | `fecha_inicio`, `fecha_fin`, `total`, `estado`, `observaciones`. |
| **Relaciones** | `curso_id` → `curso.id` (1:N); `instructor_id` → `instructor.id` (1:N); `vehiculo_id` → `vehiculo.id` (0..1:N, opcional). |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![sesion MySQL](imagenes/sesion-mysql.png) | ![sesion PostgreSQL](imagenes/sesion-postgres.png) | ![sesion SQL Server](imagenes/sesion-sqlserver.png) | ![sesion Oracle](imagenes/sesion-oracle.png) |

### 5.7 Tabla `asistencia`

| | |
|---|---|
| **Propósito** | Registra si un alumno matriculado asistió a una sesión determinada. |
| **Campos clave** | `nombre`, `descripcion`, `is_active`, `created_at`, `updated_at`. |
| **Relaciones** | `matricula_id` → `matricula.id` (1:N); `sesion_id` → `sesion.id` (1:N). |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![asistencia MySQL](imagenes/asistencia-mysql.png) | ![asistencia PostgreSQL](imagenes/asistencia-postgres.png) | ![asistencia SQL Server](imagenes/asistencia-sqlserver.png) | ![asistencia Oracle](imagenes/asistencia-oracle.png) |

### 5.8 Tabla `evaluacion`

| | |
|---|---|
| **Propósito** | Registra las evaluaciones por competencia aplicadas a un alumno, previas a su certificación. |
| **Campos clave** | `nombre`, `descripcion`, `is_active`, `created_at`, `updated_at`. |
| **Relaciones** | `matricula_id` → `matricula.id` (1:N). |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![evaluacion MySQL](imagenes/evaluacion-mysql.png) | ![evaluacion PostgreSQL](imagenes/evaluacion-postgres.png) | ![evaluacion SQL Server](imagenes/evaluacion-sqlserver.png) | ![evaluacion Oracle](imagenes/evaluacion-oracle.png) |

### 5.9 Tabla `resultado`

| | |
|---|---|
| **Propósito** | Detalla el resultado obtenido por el alumno en una evaluación específica. |
| **Campos clave** | `nombre`, `descripcion`, `is_active`, `created_at`, `updated_at`. |
| **Relaciones** | `evaluacion_id` → `evaluacion.id` (1:N). |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![resultado MySQL](imagenes/resultado-mysql.png) | ![resultado PostgreSQL](imagenes/resultado-postgres.png) | ![resultado SQL Server](imagenes/resultado-sqlserver.png) | ![resultado Oracle](imagenes/resultado-oracle.png) |

### 5.10 Tabla `certificado`

| | |
|---|---|
| **Propósito** | Representa la habilitación final del alumno, una vez cumplidos los requisitos académicos, de asistencia y de pago. |
| **Campos clave** | `nombre`, `descripcion`, `is_active`, `created_at`, `updated_at`. |
| **Relaciones** | `matricula_id UNIQUE` (opcional) → `matricula.id`, relación 0..1:1: una matrícula puede tener a lo sumo un certificado. |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![certificado MySQL](imagenes/certificado-mysql.png) | ![certificado PostgreSQL](imagenes/certificado-postgres.png) | ![certificado SQL Server](imagenes/certificado-sqlserver.png) | ![certificado Oracle](imagenes/certificado-oracle.png) |

### 5.11 Tabla `pago`

| | |
|---|---|
| **Propósito** | Registra los pagos realizados sobre una matrícula; el estado de pago condiciona el avance del alumno a nuevas etapas. |
| **Campos clave** | `referencia_tipo`, `referencia_id`, `metodo`, `monto`, `fecha`, `estado`. |
| **Relaciones** | `matricula_id` → `matricula.id` (1:N). |
| **Evidencia** | Estructura de la tabla ya creada, capturada desde el árbol de esquema de DBeaver en cada motor. |

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![pago MySQL](imagenes/pago-mysql.png) | ![pago PostgreSQL](imagenes/pago-postgres.png) | ![pago SQL Server](imagenes/pago-sqlserver.png) | ![pago Oracle](imagenes/pago-oracle.png) |

---

## 6. Datos iniciales (seed) del módulo RBAC

Evidencia de los `INSERT` iniciales de `roles` (`ADMIN`, `COORDINADOR`, `INSTRUCTOR`, `CARTERA`, `AUDITOR`) y `resources` (`POST /sesiones`, `POST /asistencias`, `POST /evaluaciones`, `POST /certificados`).

| MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| ![seed MySQL](imagenes/seed-mysql.png) | ![seed PostgreSQL](imagenes/seed-postgres.png) | ![seed SQL Server](imagenes/seed-sqlserver.png) | ![seed Oracle](imagenes/seed-oracle.png) |

## 7. Diferencias de sintaxis entre motores

| Aspecto | MySQL | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|---|
| Autoincremento | `AUTO_INCREMENT` | `SERIAL` | `IDENTITY(1,1)` | `GENERATED ALWAYS AS IDENTITY` |
| Tipo enumerado | `ENUM('ACTIVE','INACTIVE')` | tipo `ENUM` propio (`CREATE TYPE`) | `VARCHAR` + `CHECK` | `VARCHAR2` + `CHECK` |
| Texto | `VARCHAR` | `VARCHAR` | `NVARCHAR` | `VARCHAR2` |
| Fecha/hora actual | `CURRENT_TIMESTAMP` | `NOW()` | `GETDATE()` | `SYSTIMESTAMP` |
| Creación de BD | `CREATE DATABASE` | `CREATE DATABASE` (fuera de transacción) | `CREATE DATABASE` | no aplica (se usa un esquema/usuario) |

## 8. Conclusiones

Implementar el mismo esquema de VíaMaestra en cuatro motores distintos permitió comprobar que, aunque el modelo relacional (entidades, relaciones y restricciones) es idéntico en los cuatro casos, cada motor exige adaptar la sintaxis a sus propias reglas: MySQL y PostgreSQL resuelven el autoincremento y los estados (`ACTIVE`/`INACTIVE`) de forma nativa (`AUTO_INCREMENT` y `ENUM`, `SERIAL` y un tipo `ENUM` propio respectivamente), mientras que SQL Server y Oracle no tienen un tipo ENUM real y hubo que simularlo con `VARCHAR`/`VARCHAR2` más una restricción `CHECK`.

También quedó claro que "crear la base de datos" no significa lo mismo en todos los motores: en MySQL, PostgreSQL y SQL Server existe un `CREATE DATABASE` explícito, mientras que en Oracle ese concepto se traduce en trabajar dentro de un esquema/usuario ya existente, sin necesidad de crear una base nueva.

Separar el modelo en dos módulos (autenticación RBAC y negocio) facilitó reutilizar la misma lógica de permisos para los 40 proyectos de referencia, y mantener las reglas de negocio de VíaMaestra (cupos, solapamientos de sesión, requisitos antes de certificar) desacopladas del sistema de acceso, tal como pedía el enunciado del proyecto.

Como dificultad principal, migrar el script entre motores obligó a revisar caso por caso los tipos de fecha/hora (`CURRENT_TIMESTAMP`, `NOW()`, `GETDATE()`, `SYSTIMESTAMP`) y los tipos de texto (`VARCHAR` vs `NVARCHAR` vs `VARCHAR2`), ya que un mismo script no es portable entre motores sin estos ajustes.
