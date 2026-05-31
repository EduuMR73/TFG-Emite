# Emite — Plataforma SaaS de Facturación y Contabilidad

> **Emite. Cobra. Crece.**  
> Solución cloud para autónomos y PYMEs españolas: facturación Verifactu, contabilidad, open banking e IA.

[![NestJS](https://img.shields.io/badge/NestJS-11-E0234E?logo=nestjs&logoColor=white)](https://nestjs.com)
[![Angular](https://img.shields.io/badge/Angular-20-DD0031?logo=angular&logoColor=white)](https://angular.io)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.7-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org)
[![Firebase](https://img.shields.io/badge/Firebase-Auth-FFCA28?logo=firebase&logoColor=black)](https://firebase.google.com)
[![Stripe](https://img.shields.io/badge/Stripe-SaaS-635BFF?logo=stripe&logoColor=white)](https://stripe.com)
[![Ionic](https://img.shields.io/badge/Ionic-8-3880FF?logo=ionic&logoColor=white)](https://ionicframework.com)
[![Capacitor](https://img.shields.io/badge/Capacitor-8-119EFF?logo=capacitor&logoColor=white)](https://capacitorjs.com)
[![Prisma](https://img.shields.io/badge/Prisma-5-2D3748?logo=prisma&logoColor=white)](https://www.prisma.io)
[![AWS S3](https://img.shields.io/badge/AWS-S3-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/s3)
[![Tests](https://img.shields.io/badge/Tests-254%20passing-16a34a?logo=jest&logoColor=white)](https://jestjs.io)
[![License](https://img.shields.io/badge/Licencia-TFG_DAM-E8C96D)](.)

---

**Trabajo Final de Grado · Desarrollo de Aplicaciones Multiplataforma**  
IES Rafael Alberti — San Fernando, Cádiz · Curso 2025-2026

🔗 **Repositorio Backend:** [github.com/EduuMR73/TFG-APP-BACKEND](https://github.com/EduuMR73/TFG-APP-BACKEND)  
🔗 **Repositorio Frontend:** [github.com/EduuMR73/TFG-APP-FRONTEND](https://github.com/EduuMR73/TFG-APP-FRONTEND)  
🚀 **Demo en producción:** [https://emite-app.onrender.com](https://emite-app.onrender.com)

---

## 📋 Índice

1. [Descripción del Proyecto](#-descripción-del-proyecto)
2. [Arquitectura del Sistema](#-arquitectura-del-sistema)
3. [Stack Tecnológico](#-stack-tecnológico)
4. [Módulos Implementados](#-módulos-implementados)
5. [Seguridad — Cadena de Guards](#-seguridad--cadena-de-guards)
6. [Cumplimiento Verifactu (RD 1007/2023)](#-cumplimiento-verifactu-rd-10072023)
7. [Open Banking con Tink (PSD2)](#-open-banking-con-tink-psd2)
8. [IA y OCR — Anthropic Claude](#-ia-y-ocr--anthropic-claude)
9. [Facturación SaaS con Stripe](#-facturación-saas-con-stripe)
10. [Multi-empresa](#-multi-empresa)
11. [App Móvil — Capacitor + Android](#-app-móvil--capacitor--android)
12. [Cobertura de Tests](#-cobertura-de-tests)
13. [Estructura del Repositorio](#-estructura-del-repositorio)
14. [Guía de Ejecución Local](#-guía-de-ejecución-local)
15. [Limitaciones Conocidas](#️-limitaciones-conocidas)
16. [Autor](#-autor)

---

## 🎯 Descripción del Proyecto

**Emite** es una plataforma SaaS de facturación y contabilidad diseñada específicamente para el mercado español, dirigida a autónomos y pequeñas y medianas empresas. Integra en un único producto la gestión completa del ciclo económico: desde la emisión de facturas hasta la conciliación bancaria automatizada mediante inteligencia artificial.

El proyecto nace con aspiraciones comerciales reales y ha sido desarrollado íntegramente por un único desarrollador como Trabajo Final de Grado del ciclo superior de DAM.

### Highlights Técnicos

- 🔐 **Cadena de seguridad de 5 guards** en NestJS: ThrottlerGuard → FirebaseAuthGuard → CompanyMembershipGuard → SubscriptionGuard → RbacGuard
- ✅ **Cumplimiento Verifactu** (RD 1007/2023): hashing SHA-256 encadenado, inmutabilidad de facturas emitidas y facturas rectificativas
- 🏦 **Open Banking real** vía Tink API (PSD2/DSP2): conexión a bancos españoles, sincronización de movimientos y conciliación automática con IA
- 🤖 **OCR con Claude Haiku**: extracción de datos de tickets y facturas de compra mediante visión artificial
- 📊 **Modelos de impuestos españoles**: 303, 130, 111, 115, 347, 349 con cálculo automático e IRPF configurable
- 💳 **Monetización SaaS completa**: 6 planes de Stripe (9,90€ → 99,90€/mes), trial de 14 días, portal de cliente y webhooks
- 🏢 **Multi-empresa**: gestión de múltiples empresas por usuario, con aislamiento total de datos por `x-company-id`
- 📱 **App Android nativa** generada con Capacitor 8 desde la misma base de código Angular/Ionic
- 🧪 **254 tests unitarios** pasando en 19 suites Jest (100% pass rate)

---

## 🏗️ Arquitectura del Sistema

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENTE                               │
│   Angular 20 + Ionic 8   ←→   Capacitor 8 (Android APK)    │
└─────────────────────┬───────────────────────────────────────┘
                      │ HTTPS  /  x-company-id header
┌─────────────────────▼───────────────────────────────────────┐
│                  BACKEND (NestJS 11)                         │
│   ┌─────────────────────────────────────────────────────┐   │
│   │  ThrottlerGuard → FirebaseAuthGuard →               │   │
│   │  CompanyMembershipGuard → SubscriptionGuard →       │   │
│   │  RbacGuard                                          │   │
│   └──────────────────────┬──────────────────────────────┘   │
│                           │                                  │
│   ┌──────────┬────────────┼───────────────────┐             │
│   │ Facturas │ Impuestos  │ Banking / Tink     │ ...11 más   │
│   └──────────┴────────────┴───────────────────┘             │
└──────────────┬──────────────────────────────────────────────┘
               │
   ┌───────────┼──────────────────────────────────────────┐
   │           │                                          │
   ▼           ▼                                          ▼
PostgreSQL  Firebase Auth                        Servicios externos
(Prisma 5)  (tokens JWT)                  Stripe · Tink · AWS S3 · Claude
```

**Modelo multi-tenant:** cada petición autenticada lleva el header `x-company-id`. La capa de servicio filtra todos los registros por `companyId` automáticamente. El soft-delete vía extensión de Prisma garantiza el trazado de auditoría requerido por Verifactu.

---

## 🛠️ Stack Tecnológico

### Backend

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| Framework | NestJS | 11.0.1 | API REST modular, inyección de dependencias |
| Lenguaje | TypeScript | 5.7.3 | Tipado estático, decoradores |
| ORM | Prisma | 5.10.2 | Acceso a base de datos, migraciones, soft-delete |
| Base de datos | PostgreSQL | 15 | Persistencia multi-tenant |
| Autenticación | Firebase Admin SDK | 13.6.1 | Verificación de tokens JWT |
| Pagos | Stripe SDK | 20.4.0 | Suscripciones SaaS, webhooks, portal de cliente |
| Open Banking | Tink API | custom | Conexión bancaria PSD2 (sandbox) |
| IA / OCR | Anthropic SDK (`@anthropic-ai/sdk`) | 0.78.0 | Claude Haiku para visión artificial |
| Almacenamiento | AWS S3 | SDK v3 | Documentos, PDFs, facturas |
| CDN imágenes | Cloudinary | 2.9.0 | Logos de empresa optimizados |
| Emails | Resend | 6.12.3 | Notificaciones transaccionales |
| PDF | PDFKit | 0.17.2 | Generación de facturas en PDF |
| Decimales | Decimal.js | 10.6.0 | Aritmética monetaria sin errores de coma flotante |
| Tests | Jest + ts-jest | 30.0.0 | 254 tests unitarios en 19 suites |

### Frontend

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| Framework | Angular | 20.0.0 | SPA, standalone components |
| UI | Ionic | 8.0.0 | Componentes adaptados a web y móvil |
| Móvil | Capacitor | 8.3.1 | APK Android nativo |
| Auth | @angular/fire | 20.0.1 | Firebase Auth en frontend |
| Reactividad | RxJS | 7.8.0 | Observables, interceptores |
| Gráficos | Chart.js | 4.5.1 | Dashboard de KPIs y métricas |
| Export | xlsx | 0.18.5 | Exportación a Excel |
| Estilos | SCSS + CSS Variables | — | Design system Champán + Carbón |

### Infraestructura

| Servicio | Uso |
|---------|-----|
| Render (Frankfurt) | Backend NestJS + Frontend Angular |
| PostgreSQL en Render | Base de datos de producción |
| Firebase (Google) | Autenticación de usuarios |
| AWS S3 | Almacenamiento de documentos |
| Cloudinary | CDN de imágenes |
| Stripe | Facturación SaaS |
| Tink | Open banking (sandbox) |
| UptimeRobot | Monitorización de disponibilidad |

---

## 📦 Módulos Implementados

| # | Módulo | Descripción |
|---|--------|-------------|
| 1 | **Auth & Onboarding** | Registro, login (email + Google), onboarding de empresa, recuperación de contraseña |
| 2 | **Dashboard** | KPIs financieros, resumen de ingresos/gastos, gráficos por período |
| 3 | **Facturas** | Emisión (Verifactu), borradores, proforma, recurrentes, rectificativas, cobros parciales/totales |
| 4 | **Presupuestos** | Creación, conversión a factura, seguimiento de estado |
| 5 | **Compras / Gastos** | Registro manual y por OCR (foto del ticket), categorización, IVA/IRPF deducible |
| 6 | **Contactos** | Clientes y proveedores, autocompletado de NIF/CIF |
| 7 | **Productos** | Catálogo de productos y servicios, tarifas por contacto |
| 8 | **Impuestos** | Modelos 303, 130, 111, 115, 347 y 349 con cálculo automático e IRPF |
| 9 | **Movimientos** | Conexión bancaria Tink, importación de transacciones, conciliación con IA |
| 10 | **Multi-empresa** | Gestión de múltiples empresas por cuenta, cambio de contexto en tiempo real |
| 11 | **Configuración** | Datos fiscales, logo, series de facturación, IBAN/BIC, planes Stripe |

---

## 🔐 Seguridad — Cadena de Guards

Cada petición autenticada pasa obligatoriamente por los cinco guards registrados como `APP_GUARD` en `AppModule`, en este orden:

```
Request
   │
   ▼
ThrottlerGuard          ← 10 req/min global (200/min para endpoints normales)
   │
   ▼
FirebaseAuthGuard       ← Verifica Bearer token Firebase, crea/recupera usuario en BD
   │  req.user = { id, email, uid, isSuperAdmin }
   ▼
CompanyMembershipGuard  ← Valida header x-company-id y membresía del usuario
   │  req.companyId, req.userRole
   ▼
SubscriptionGuard       ← Comprueba estado: TRIAL / ACTIVE / PAST_DUE / EXPIRED / CANCELLED
   │  402 si trial expirado, 403 si cancelado
   ▼
RbacGuard               ← Verifica @RequirePermission() con matriz de roles y permisos
   │
   ▼
Controller → Service → Prisma → PostgreSQL
```

**Roles:** `OWNER` → `ADMIN` → `VIEWER`  
**Feature keys:** `invoice:create`, `expense:read`, `contact:update`... (granulares por acción)  
**Soft delete:** Extensión de Prisma intercepta `.findMany()` / `.findUnique()` / `.update()` para excluir registros con `deletedAt != null` de forma automática.

---

## 📜 Cumplimiento Verifactu (RD 1007/2023)

Emite implementa los requisitos técnicos del Reglamento de Facturación (RD 1007/2023) que entró en vigor en 2026:

| Requisito | Implementación |
|-----------|---------------|
| **Inmutabilidad** | Las facturas emitidas (`ISSUED`) son inmutables. Cualquier modificación requiere una factura rectificativa |
| **Hashing encadenado** | Cada factura almacena su huella SHA-256 y la huella de la factura anterior |
| **Facturas rectificativas** | Flujo `ISSUED → RECTIFIED`, nueva factura `RECTIFICATIVE` con cantidades negativas y prefijo `[RECTIFICACIÓN]` |
| **Soft delete** | Las facturas nunca se eliminan físicamente; `deletedAt` preserva el trazado de auditoría |
| **Numeración correlativa** | Series de facturación configurables con numeración secuencial garantizada |

---

## 🏦 Open Banking con Tink (PSD2)

La integración con **Tink** (plataforma PSD2 de Visa) permite a los usuarios conectar sus cuentas bancarias directamente desde Emite:

```
Usuario
   │  POST /banking/connect
   ▼
Backend genera Tink Link URL
   │
   ▼ Redirige al usuario
Tink Link (bank selector + OAuth bancario)
   │  Código de autorización
   ▼
Backend: intercambia código → token de usuario Tink
   │
   ▼  Sincronización cada 6 horas (cron job)
Fetch de transacciones → BankTransaction en PostgreSQL
   │
   ▼  Pipeline de conciliación
L1: Match exacto (importe + referencia)
L2: Match combinatorial (varios movimientos → una factura)
L3: Claude Haiku (IA semántica para casos ambiguos)
```

> **Nota:** La integración Tink está en entorno **sandbox**. La conexión a bancos reales requiere aprobación de Tink y cuenta de producción.

---

## 🤖 IA y OCR — Anthropic Claude

El módulo de gastos integra **Claude Haiku** (`claude-haiku-4-5`) para dos casos de uso:

### 1. OCR de tickets y facturas de compra
El usuario fotografía un ticket desde la app móvil (cámara nativa via `@capacitor/camera`) o sube una imagen en web. El backend envía la imagen en base64 a la API de visión de Claude y extrae automáticamente:

- Proveedor / razón social
- NIF/CIF del emisor
- Fecha de la operación
- Base imponible, IVA e IRPF
- Importe total
- Categoría del gasto

**Protección contra prompt injection:** se aplica un allowlist de campos permitidos (`ALLOWED_OCR_FIELDS`) para ignorar instrucciones maliciosas que pudieran estar impresas en el ticket.

### 2. Conciliación bancaria inteligente (nivel L3)
Cuando los niveles de matching exacto (L1) y combinatorial (L2) no encuentran correspondencia, Claude analiza semánticamente la descripción del movimiento bancario y la compara con las facturas pendientes del período.

---

## 💳 Facturación SaaS con Stripe

Emite es un SaaS completamente monetizado con seis planes de suscripción:

| Plan | Precio/mes | Facturas | Usuarios | Empresas | OCR |
|------|-----------|---------|---------|---------|-----|
| Autónomo Basic | 9,90 € | 50 | 1 | 1 | 10 |
| Autónomo Pro | 19,90 € | 200 | 2 | 1 | 50 |
| Autónomo Premium | 29,90 € | ∞ | 3 | 2 | 200 |
| PYME Basic | 39,90 € | ∞ | 5 | 3 | 500 |
| PYME Pro | 59,90 € | ∞ | 10 | 5 | ∞ |
| PYME Enterprise | 99,90 € | ∞ | ∞ | ∞ | ∞ |

- **Trial gratuito de 14 días** sin necesidad de tarjeta de crédito
- Checkout y portal de cliente Stripe con branding de Emite (color champán `#E8C96D`)
- Ciclos mensual y anual
- Webhooks para sincronización en tiempo real del estado de suscripción
- `UsageLimitGuard` bloquea los endpoints cuando se supera el límite del plan

---

## 🏢 Multi-empresa

Un usuario puede gestionar varias empresas desde la misma cuenta:

- Cada empresa tiene sus propios datos fiscales, series de facturación, logo e IBAN
- El cambio de empresa activa se gestiona mediante el `CompanyService` del frontend (localStorage + token)
- La API aísla todos los datos mediante el header `x-company-id` validado en el guard
- El plan del usuario define cuántas empresas puede crear (`maxCompanies` por plan)
- Se controla que el número de empresas no supere el límite incluso durante el trial

---

## 📱 App Móvil — Capacitor + Android

La misma base de código Angular/Ionic se compila como APK nativa para Android:

```bash
# Build de la app web
ng build --configuration production

# Sincronizar con Capacitor
npx cap sync android

# Abrir en Android Studio
npx cap open android
```

**App ID:** `com.emite.saas`  
**Plugins nativos utilizados:**

| Plugin | Uso |
|--------|-----|
| `@capacitor/camera` | Captura de tickets para OCR |
| `@capacitor/filesystem` | Exportación de Excel a almacenamiento local |
| `@capacitor/share` | Compartir PDF de factura (share sheet nativo) |
| `@capacitor/keyboard` | Control del teclado en formularios |
| `@capacitor/haptics` | Feedback háptico en acciones |
| `@capacitor/splash-screen` | Pantalla de carga de la app |
| `@capacitor/app` | Gestión del botón atrás nativo |

**Diseño responsive:** las tablas de listado se transforman en tarjetas apiladas en móvil. El sidebar de escritorio se convierte en un drawer con hamburguesa. Las acciones de formulario están optimizadas para teclado nativo (`enterkeyhint`, `Keyboard.hide()`).

---

## 🧪 Cobertura de Tests

```
Test Suites: 19 passed, 19 total
Tests:       254 passed, 254 total
Snapshots:   0 total
Time:        ~7.6 s
```

| Área | Suites | Tests |
|------|--------|-------|
| Auth / Claims / Guards | 3 | ~45 |
| Facturas / Pagos | 2 | ~40 |
| Gastos / OCR | 1 | ~20 |
| Banking / Conciliación | 2 | ~35 |
| Billing / Trial / Eventos | 3 | ~30 |
| Prisma / Verifactu / Seguridad | 2 | ~25 |
| UsageLimitGuard | 3 | ~35 |
| Email / Admin / App | 3 | ~24 |

**Tecnología:** Jest 30 + ts-jest 29. Los mocks de Prisma simulan transacciones interactivas pasando el propio mock como `tx`. Las llamadas a Stripe, Tink, Anthropic y Firebase están completamente mockeadas.

---

## 📁 Estructura del Repositorio

El proyecto está dividido en dos repositorios independientes:

### Backend (`emite-backend`)
```
src/
├── main.ts                    # Bootstrap: Express middleware, Swagger, ValidationPipe
├── app.module.ts              # Módulo raíz: guard chain, 20+ feature modules
├── auth/                      # Firebase guards, RBAC, decoradores
│   ├── guards/                # ThrottlerGuard, FirebaseAuthGuard, etc.
│   ├── rbac/                  # feature-keys.ts, role-permission matrix
│   └── decorators/            # @Public(), @CurrentUser(), @CompanyId()
├── prisma/                    # PrismaService con extensión soft-delete
├── common/                    # Configuración de planes, UsageLimitGuard
├── invoices/                  # Facturas, líneas, pagos, Verifactu
├── quotes/                    # Presupuestos
├── modules/
│   ├── billing/               # Stripe: checkout, webhooks, portal
│   ├── expenses/              # Gastos manuales y por OCR
│   ├── email/                 # Notificaciones por email (Resend)
│   └── documents/             # S3, PDF (PDFKit), Cloudinary
├── banking/                   # Tink API, sync de transacciones
├── tax-reports/               # Modelos 303, 130, 111, 115, 347, 349
├── ocr/                       # Anthropic Claude Vision
├── contacts/                  # Clientes y proveedores
├── products/                  # Catálogo de productos
├── companies/                 # Multi-empresa
└── dashboard/                 # KPIs y métricas
prisma/
└── schema.prisma              # Fuente única de verdad del esquema de BD
```

### Frontend (`emite-frontend`)
```
src/app/
├── core/
│   ├── services/              # AuthService, CompanyService
│   └── guards/                # authGuard
├── interceptors/              # auth.interceptor.ts (JWT + x-company-id)
├── services/                  # invoice.service.ts, contact.service.ts...
├── shared/                    # LimitReachedModal, validators
└── pages/
    ├── shell/                 # App shell, sidebar, company switcher
    ├── dashboard/             # KPIs, gráficos Chart.js
    ├── ventas/                # Facturas, presupuestos, proforma, recurrentes
    ├── compras/               # Gastos + OCR
    ├── contactos/             # Contactos
    ├── productos/             # Catálogo
    ├── impuestos/             # Modelos fiscales
    ├── movimientos/           # Open banking, conciliación
    └── configuracion/         # Empresa, perfil fiscal, Stripe, IBAN
```

---

## 🚀 Guía de Ejecución Local

### Requisitos Previos

| Herramienta | Versión mínima |
|------------|---------------|
| Node.js | 22.x |
| npm | 10.x |
| Angular CLI | 20.x |
| PostgreSQL | 15.x (o externa) |
| Git | 2.x |

### 1. Backend

```bash
# Clonar repositorio backend
git clone https://github.com/EduuMR73/TFG-APP-BACKEND.git
cd TFG-APP-BACKEND

# Instalar dependencias
npm install

# Configurar variables de entorno
cp .env.example .env
# Editar .env con tus claves de Firebase, Stripe, AWS, Tink y Anthropic

# Generar cliente Prisma y aplicar esquema
npx prisma generate
npx prisma db push

# (Opcional) Poblar BD con datos de demo
npm run seed

# Iniciar servidor de desarrollo
npm run start:dev
```

Backend disponible en `http://localhost:3001`  
Swagger/OpenAPI en `http://localhost:3001/api/docs`

### 2. Frontend

```bash
# Clonar repositorio frontend
git clone https://github.com/EduuMR73/TFG-APP-FRONTEND.git
cd TFG-APP-FRONTEND

# Instalar dependencias
npm install

# Apuntar al backend local (src/environments/environment.ts)
# apiUrl: 'http://localhost:3001'

# Iniciar servidor de desarrollo
npm start
```

Frontend disponible en `http://localhost:8100`

### 3. Variables de Entorno (Backend)

```env
# Base de datos
DATABASE_URL=postgresql://usuario:password@localhost:5432/emite

# Firebase
FIREBASE_PROJECT_ID=tu-proyecto
FIREBASE_CLIENT_EMAIL=tu-service-account@proyecto.iam.gserviceaccount.com
FIREBASE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n"

# AWS S3
AWS_REGION=eu-west-1
AWS_ACCESS_KEY_ID=AKIA...
AWS_SECRET_ACCESS_KEY=...
AWS_S3_BUCKET=emite-documents

# Stripe
STRIPE_SECRET_KEY=sk_test_...
STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Tink (Open Banking)
TINK_CLIENT_ID=...
TINK_CLIENT_SECRET=...
TINK_BASE_URL=https://api.tink.com

# Anthropic (OCR)
ANTHROPIC_API_KEY=sk-ant-...

# Email
RESEND_API_KEY=re_...

# General
PORT=3001
NODE_ENV=development
```

---

## ⚠️ Limitaciones Conocidas

| Limitación | Motivo |
|-----------|--------|
| **Sin app iOS** | Requiere Mac + cuenta de desarrollador Apple (99 $/año) |
| **Sin envío a AEAT** | La presentación directa de modelos fiscales requiere integración oficial con la Agencia Tributaria |
| **Tink en sandbox** | La conexión a bancos reales requiere aprobación comercial de Tink/Visa |
| **Sin internacionalización (i18n)** | La plataforma está optimizada para el mercado español |
| **Autocompletado NIF** | Las APIs públicas gratuitas (api.cifnif.es) tienen cobertura limitada; la integración con Axesor o API Registers está prevista para producción |
| **Teclado Huawei EMUI** | Los eventos de teclado no se disparan correctamente en EMUI con `resize:'none'`; se recomienda el uso via navegador en dispositivos Huawei |

---

## 👤 Autor

**Eduardo Moreno Reinoso**

Estudiante de 2.º DAM — Desarrollo de Aplicaciones Multiplataforma  
IES Rafael Alberti · San Fernando, Cádiz

- 🐙 GitHub: [@EduuMR73](https://github.com/EduuMR73)

---

> *Documentación actualizada: Mayo 2026 · Versión 1.0.0 — TFG DAM 2025-2026*
