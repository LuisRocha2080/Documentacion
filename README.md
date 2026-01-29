# 🗂️ PromptVault

<p align="center">
  <img src="https://img.shields.io/badge/Laravel-12.0-FF2D20?style=for-the-badge&logo=laravel&logoColor=white" alt="Laravel 12">
  <img src="https://img.shields.io/badge/PHP-8.2+-777BB4?style=for-the-badge&logo=php&logoColor=white" alt="PHP 8.2+">
  <img src="https://img.shields.io/badge/TailwindCSS-3.1-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white" alt="TailwindCSS">
  <img src="https://img.shields.io/badge/Alpine.js-3.4-8BC0D0?style=for-the-badge&logo=alpine.js&logoColor=white" alt="Alpine.js">
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="License MIT">
</p>

## 📋 Descripción

**PromptVault** es un sistema web avanzado de gestión de prompts con integración multi-provider de IA, diseñado bajo principios **SOLID** y arquitectura limpia. Permite a los usuarios crear, compartir, calificar y versionar prompts, además de interactuar con múltiples modelos de IA (Claude, Gemini, Groq) en tiempo real.

### ✨ Características Principales

- 🤖 **Multi-Provider IA**: Claude (Anthropic), Gemini (Google AI Studio), Groq
- 📝 **Gestión de Prompts**: CRUD completo con versionado automático
- 🔐 **Sistema de Permisos**: Basado en Policies y Gates de Laravel
- ⭐ **Calificaciones y Comentarios**: Sistema de valoración comunitario
- 🏷️ **Etiquetado Inteligente**: Organización con tags personalizables
- 🔄 **Compartir Prompts**: Accesos temporales con permisos granulares
- 📊 **Dashboard Analítico**: Métricas de uso y tendencias
- 🎨 **Interfaz Moderna**: TailwindCSS + Alpine.js

---

## 🛠️ Herramientas Utilizadas

El desarrollo de PromptVault se sustenta en un stack tecnológico moderno y robusto, optimizado para escalabilidad y mantenibilidad.

### 🎯 Entorno de Desarrollo

| Herramienta | Versión | Propósito |
|------------|---------|-----------|
| **PHP** | 8.2+ | Lenguaje backend con tipado fuerte y modern syntax |
| **Composer** | 2.x | Gestor de dependencias PHP |
| **Node.js** | 18+ | Runtime JavaScript para build tools |
| **NPM** | 9.x | Gestor de paquetes frontend |
| **Git** | 2.x | Control de versiones |

### 🧪 Testing y Calidad de Código

| Herramienta | Descripción |
|------------|-------------|
| **PHPUnit** `^11.5` | Framework de testing unitario e integración |
| **Laravel Pint** `^1.24` | Code formatter basado en PHP-CS-Fixer |
| **Mockery** `^1.6` | Mocking framework para tests |
| **Faker** `^1.23` | Generación de datos ficticios |

### 📦 Dependencias Backend

```json
{
  "laravel/framework": "^12.0",
  "laravel/tinker": "^2.10",
  "lucianotonet/groq-php": "^1.3"
}
```

### 🎨 Dependencias Frontend

```json
{
  "tailwindcss": "^3.1.0",
  "@tailwindcss/forms": "^0.5.2",
  "alpinejs": "^3.4.2",
  "vite": "^7.0.7",
  "axios": "^1.11.0",
  "fullcalendar": "^6.1.20"
}
```

### 🔧 Herramientas de Despliegue

- **Laravel Sail** `^1.41`: Entorno Docker para desarrollo local
- **Nixpacks**: Build & deployment en Railway/Nixpacks-compatible platforms
- **Vite** `^7.0`: Build tool ultra-rápido con HMR

---

## 🚀 Framework Utilizado

### Laravel 12.0 - The PHP Framework for Web Artisans

#### 🎯 Justificación de Elección

Laravel ha sido seleccionado como framework principal por las siguientes razones técnicas:

#### 1️⃣ **Escalabilidad Empresarial**
- **Service Container**: Inyección de dependencias nativa para arquitectura SOLID
- **Service Providers**: Modularización y lazy loading de componentes
- **Queue System**: Procesamiento asíncrono de tareas pesadas (backups, notificaciones)
- **Cache Drivers**: Soporte multi-backend (Redis, Memcached, Database)

```php
// Ejemplo de arquitectura limpia en PromptVault
class PromptController extends Controller
{
    public function __construct(
        private PromptService $promptService,
        private CalificacionService $calificacionService
    ) {}
}
```

#### 2️⃣ **Seguridad de Primera Clase**
- **Eloquent ORM**: Protección contra SQL Injection mediante Query Builder
- **CSRF Protection**: Tokens automáticos en formularios
- **Mass Assignment Protection**: `$fillable` y `$guarded` en modelos
- **Policies & Gates**: Control de acceso granular (implementado en 100% del módulo Prompts)
- **Encryption**: Cifrado AES-256-CBC nativo

```php
// Policy implementada en PromptVault
class PromptPolicy
{
    public function update(User $user, Prompt $prompt): bool
    {
        return $user->id === $prompt->user_id 
            || $user->hasRole('admin');
    }
}
```

#### 3️⃣ **Mantenibilidad y Developer Experience**
- **Blade Templating**: Sintaxis limpia con componentes reutilizables
- **Artisan CLI**: 50+ comandos nativos + comandos personalizados
- **Migrations & Seeders**: Control de versiones de base de datos
- **Testing Suite**: PHPUnit integrado con factories y traits

```bash
# Comandos personalizados en PromptVault
php artisan check:models          # Listar modelos IA disponibles
php artisan app:verify-ai-keys    # Verificar claves API
```

#### 4️⃣ **Compatibilidad con Requerimientos**
- ✅ **Multi-tenancy**: Soporte para múltiples usuarios con roles
- ✅ **REST API**: Recursos y middlewares para API RESTful
- ✅ **Real-time**: Broadcasting con Pusher/Socket.io
- ✅ **File Storage**: Abstracción de filesystems (local, S3, FTP)
- ✅ **Internationalization**: Sistema i18n robusto

#### 📊 Comparativa con Alternativas

| Característica | Laravel 12 | Symfony 7 | CodeIgniter 4 |
|---------------|------------|-----------|---------------|
| Curva de aprendizaje | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| ORM nativo | Eloquent | Doctrine | Sin ORM |
| Testing integrado | ✅ PHPUnit | ✅ PHPUnit | ✅ PHPUnit |
| Ecosistema | 🔥 Amplio | 🔥 Amplio | 🟡 Limitado |
| Performance | Alta | Muy alta | Muy alta |
| Comunidad | 🔥 Muy activa | 🟢 Activa | 🟡 Moderada |

#### 🏗️ Arquitectura Implementada

```
┌─────────────────────────────────────────────┐
│           HTTP Request (web.php)            │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  Middleware (auth, csrf, policies)          │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  Controllers (thin, solo coordinación)      │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  Services (lógica de negocio)               │
│  - PromptService                            │
│  - CalificacionService                      │
│  - ChatbotService                           │
│  - CompartirService                         │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  Repositories (acceso a datos)              │
│  - PromptRepository                         │
│  - ChatbotGeminiRepository                  │
│  - ChatbotClaudeRepository                  │
└─────────────────┬───────────────────────────┘
                  │
┌─────────────────▼───────────────────────────┐
│  Models (Eloquent ORM)                      │
└─────────────────────────────────────────────┘
```

---

## 🎨 Herramientas de Interacción Visual

### Frontend Stack

#### **TailwindCSS 3.1** - Utility-First CSS Framework

##### 🎯 ¿Por qué TailwindCSS?

- **Desarrollo Rápido**: Clases utilitarias predefinidas (`flex`, `grid`, `bg-blue-500`)
- **Consistencia**: Sistema de diseño coherente sin CSS custom
- **Bundle Optimizado**: PurgeCSS elimina estilos no utilizados (build < 10KB)
- **Responsive First**: Breakpoints móviles nativos (`sm:`, `md:`, `lg:`, `xl:`)
- **Dark Mode**: Soporte nativo con clase `dark:`

```html
<!-- Ejemplo de componente en PromptVault -->
<button class="px-4 py-2 bg-gradient-to-r from-blue-500 to-purple-600 
               text-white rounded-lg hover:scale-105 
               transition-transform duration-200 
               shadow-lg hover:shadow-xl">
    Guardar Prompt
</button>
```

##### 🧩 Plugins Utilizados
- **@tailwindcss/forms**: Estilos preconstruidos para formularios
- **@tailwindcss/vite**: Integración optimizada con Vite

#### **Alpine.js 3.4** - Lightweight JavaScript Framework

##### 💡 Reactvidad Sin Overhead

Alpine.js proporciona interactividad declarativa similar a Vue.js pero con una huella mínima (15KB gzipped).

```html
<!-- Widget de calificación con Alpine.js -->
<div x-data="{ rating: 0, hovering: 0 }">
    <template x-for="star in 5" :key="star">
        <button @click="rating = star" 
                @mouseenter="hovering = star"
                @mouseleave="hovering = 0"
                :class="star <= (hovering || rating) ? 'text-yellow-400' : 'text-gray-300'">
            ★
        </button>
    </template>
</div>
```

**Casos de Uso en PromptVault:**
- Modales y dropdowns dinámicos
- Validación de formularios en tiempo real
- Filtros de búsqueda interactivos
- Tooltips y notificaciones

#### **Blade Components** - Sistema de Componentes de Laravel

##### 🧱 Reutilización y Mantenibilidad

```php
<!-- resources/views/components/form-label.blade.php -->
@props(['required' => false])

<label {{ $attributes->merge(['class' => 'block text-sm font-medium text-gray-700']) }}>
    {{ $slot }}
    @if($required)
        <span class="text-red-500">*</span>
    @endif
</label>
```

**Uso:**
```html
<x-form-label for="title" required>Título del Prompt</x-form-label>
```

#### **Vite 7.0** - Next Generation Frontend Tooling

##### ⚡ Build Ultrarrápido

- **Hot Module Replacement (HMR)**: Cambios instantáneos sin refresh
- **Code Splitting**: Chunks optimizados por ruta
- **Tree Shaking**: Eliminación de código muerto
- **CSS Modules**: Scoping automático de estilos

```javascript
// vite.config.js
export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
});
```

#### **FullCalendar 6.1** - Visualización de Eventos

Biblioteca para calendario interactivo utilizado en el módulo de eventos y seguimiento de actividades.

#### 🎨 Diseño y Prototipado

| Herramienta | Uso en el Proyecto |
|------------|-------------------|
| **Mockups HTML** | Wireframes navegables en `/mockups/prompt/` |
| **Diagramas ER** | Documentación de base de datos en `/DiagramasHTML/` |
| **Blade Layouts** | Sistema de plantillas maestras |

#### 📱 Principios UX/UI Implementados

1. **Mobile-First**: Diseño responsive desde breakpoint `sm:`
2. **Accesibilidad (a11y)**: Labels semánticos, contraste WCAG 2.1 AA
3. **Feedback Visual**: Estados hover, focus, disabled
4. **Micro-interacciones**: Transiciones suaves con `transition-*`
5. **Loading States**: Spinners y skeletons en peticiones async

---

## 📦 Instalación

### Requisitos Previos

- PHP >= 8.2
- Composer
- Node.js >= 18
- MySQL/PostgreSQL
- Claves API (Claude, Gemini, Groq)

### Pasos

```bash
# 1. Clonar repositorio
git clone https://github.com/tu-usuario/promptvault.git
cd promptvault

# 2. Setup automático (instala dependencias y configura .env)
composer setup

# 3. Configurar variables de entorno
cp .env.example .env
# Editar .env con tus credenciales de base de datos y APIs

# 4. Ejecutar migraciones y seeders
php artisan migrate --seed

# 5. Iniciar servidor de desarrollo
php artisan serve

# En otra terminal:
npm run dev
```

### Variables de Entorno Clave

```env
# APIs de IA
ANTHROPIC_API_KEY=sk-ant-xxx
GOOGLE_AI_API_KEY=AIzaSyxxx
GROQ_API_KEY=gsk_xxx

# Base de Datos
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=promptvault
```

---

## 🧪 Testing

```bash
# Ejecutar todos los tests
php artisan test

# Tests con coverage
php artisan test --coverage

# Tests específicos
php artisan test --filter=PromptTest
```

**Cobertura Actual:**
- ✅ Unit Tests: Models, Services, Repositories
- ✅ Feature Tests: HTTP, Policies, Integración
- 📊 Coverage: > 85% del código crítico

---

## 📚 Documentación Adicional

- [Plan de Testing](docs/fase4-plan-integral-testing.md)
- [Migración CSS a Tailwind](docs/docs-migration-css-to-tailwind.md)
- [Auditoría de Seguridad](docs/fase1-auditoria-seguridad-implementacion-prompts.md)
- [Diagramas ER](DiagramasHTML/ER_diagrama.html)

---

## 🤝 Contribución

Las contribuciones son bienvenidas. Por favor:

1. Fork el proyecto
2. Crea una rama feature (`git checkout -b feature/AmazingFeature`)
3. Ejecuta `./vendor/bin/pint` antes de commit
4. Commit en español con Conventional Commits (`git commit -m 'feat: nueva funcionalidad'`)
5. Push a la rama (`git push origin feature/AmazingFeature`)
6. Abre un Pull Request

---

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver [LICENSE](LICENSE) para más detalles.

---

## 👥 Equipo

Desarrollado con ❤️ siguiendo principios **SOLID** y **Clean Code**.

---

## 🔗 Enlaces

- [Laravel Documentation](https://laravel.com/docs/12.x)
- [TailwindCSS Docs](https://tailwindcss.com/docs)
- [Alpine.js Guide](https://alpinejs.dev)
- [Anthropic API](https://docs.anthropic.com)
- [Google AI Studio](https://ai.google.dev)
