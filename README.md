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

## 🎨 Proceso del Prototipado Inicial

### Metodología Design-First

El desarrollo de PromptVault siguió un enfoque **Figma → Código**, garantizando consistencia visual antes de la implementación.

#### 🔄 Flujo de Diseño

```mermaid
Investigación UX → Wireframes → Prototipo Figma → Validación → Desarrollo
```

| Fase | Herramienta | Entregable |
|------|------------|------------|
| **1. Wireframing** | Figma (baja fidelidad) | Estructura de páginas y navegación |
| **2. UI Design** | Figma (alta fidelidad) | Sistema de diseño completo |
| **3. Prototyping** | Figma Interactive | Flujos navegables con transiciones |
| **4. Handoff** | Figma Dev Mode | Especificaciones CSS y assets |

#### 🎯 Validación Pre-Desarrollo

**Páginas Prototipadas:**
- ✅ Dashboard principal con métricas
- ✅ CRUD de prompts (crear/editar/listar)
- ✅ Vista detalle con versionado
- ✅ Sistema de calificación y comentarios
- ✅ Chat con IA (multi-provider)
- ✅ Panel de administración

**Elementos Interactivos:**
- Navegación entre vistas
- Estados de formularios (vacío/error/éxito)
- Modales y dropdowns
- Responsive breakpoints (mobile/tablet/desktop)

---

## 🎨 Elementos Visuales del Sistema

### Sistema de Diseño PromptVault

#### 🎨 Paleta de Colores

```css
/* Colores Primarios */
--blue-500: #3B82F6;    /* Acciones principales */
--purple-600: #9333EA;  /* Gradientes y énfasis */
--gray-900: #111827;    /* Textos principales */

/* Colores Semánticos */
--success: #10B981;     /* Operaciones exitosas */
--warning: #F59E0B;     /* Alertas */
--error: #EF4444;       /* Errores y validaciones */
--info: #3B82F6;        /* Información contextual */

/* Neutrales */
--gray-50 → --gray-900  /* Escala completa TailwindCSS */
```

**Aplicación:**
- **Botones CTA**: Gradiente blue-500 → purple-600
- **Navegación**: Gray-800 con hover blue-500
- **Badges**: Colores semánticos según tipo

#### 🔤 Tipografía

| Elemento | Font Family | Tamaño | Peso |
|----------|------------|--------|------|
| **Headings (h1-h3)** | Inter | 2xl-4xl | 700 |
| **Body** | Inter | base | 400 |
| **Code** | JetBrains Mono | sm | 500 |
| **Labels** | Inter | sm | 500 |

```html
<!-- Ejemplo de jerarquía -->
<h1 class="text-4xl font-bold text-gray-900">Título Principal</h1>
<h2 class="text-2xl font-semibold text-gray-800">Subtítulo</h2>
<p class="text-base text-gray-600">Párrafo de contenido</p>
```

#### 🔲 Iconografía

**Sistema:** Heroicons 2.0 (línea y sólido)

```html
<!-- Ejemplos de iconos en uso -->
<svg class="w-5 h-5"><!-- icon-sparkles (IA) --></svg>
<svg class="w-5 h-5"><!-- icon-folder (Prompts) --></svg>
<svg class="w-5 h-5"><!-- icon-star (Calificación) --></svg>
```

**Tamaños Estándar:**
- **Botones**: 5×5 (20px)
- **Navegación**: 6×6 (24px)
- **Hero Icons**: 8×8 (32px)

#### 📐 Espaciado y Layout

**Sistema de Grid:**
```html
<!-- Layout principal -->
<div class="grid grid-cols-12 gap-6">
    <aside class="col-span-3"><!-- Sidebar --></aside>
    <main class="col-span-9"><!-- Contenido --></main>
</div>
```

**Espaciado Consistente:**
- **Padding interno**: `p-4`, `p-6` (componentes)
- **Margen entre secciones**: `my-8`, `my-12`
- **Gap en grids**: `gap-4`, `gap-6`

#### 🎭 Componentes Reutilizables

| Componente | Descripción | Archivo |
|-----------|-------------|---------|
| **x-form-label** | Label con asterisco requerido | `components/form-label.blade.php` |
| **x-form-select** | Select estilizado con Tailwind | `components/form-select.blade.php` |
| **x-button** | Botón con variantes (primary/secondary) | `components/button.blade.php` |
| **x-modal** | Modal Alpine.js reutilizable | `components/modal.blade.php` |
| **x-alert** | Alertas semánticas | `components/alert.blade.php` |

---

## 💻 Traducción del Prototipo a Código

### De Figma a Laravel

#### 🔀 Proceso de Conversión

```
Figma Design → Export Assets → Blade Components → TailwindCSS → Alpine.js
```

**Ejemplo Práctico:**

**1️⃣ Diseño en Figma:**
- Card de prompt con sombra
- Padding: 24px
- Border radius: 12px
- Shadow: 0px 4px 12px rgba(0,0,0,0.1)

**2️⃣ Traducción a Tailwind:**
```html
<div class="bg-white rounded-xl shadow-lg p-6">
    <!-- Contenido del prompt -->
</div>
```

**3️⃣ Componente Blade:**
```php
<!-- resources/views/components/prompt-card.blade.php -->
@props(['prompt'])

<div class="bg-white rounded-xl shadow-lg hover:shadow-xl transition-shadow p-6">
    <h3 class="text-xl font-bold text-gray-900">{{ $prompt->titulo }}</h3>
    <p class="text-gray-600 mt-2">{{ Str::limit($prompt->contenido, 150) }}</p>
    
    <div class="flex items-center gap-4 mt-4">
        <x-badge color="blue">{{ $prompt->etiquetas->count() }} tags</x-badge>
        <span class="text-sm text-gray-500">{{ $prompt->created_at->diffForHumans() }}</span>
    </div>
</div>
```

---

### 7.6.1 HTML - Estructura Semántica

#### 🏗️ Arquitectura de Vistas

**Jerarquía Blade:**

```
layouts/
├── app.blade.php           # Layout maestro (header, nav, footer)
├── admin.blade.php         # Layout admin con sidebar
└── guest.blade.php         # Layout público (login/register)

prompts/
├── index.blade.php         # Listado (tabla/grid)
├── show.blade.php          # Detalle individual
├── create.blade.php        # Formulario creación
└── edit.blade.php          # Formulario edición
```

#### 📄 Estructura HTML Semántica

```html
<!-- resources/views/prompts/show.blade.php -->
<x-app-layout>
    <x-slot name="header">
        <h2 class="text-2xl font-semibold">{{ $prompt->titulo }}</h2>
    </x-slot>

    <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
            <!-- Contenido principal -->
            <article class="bg-white rounded-lg shadow-md p-8">
                <header>
                    <h1 class="text-3xl font-bold">{{ $prompt->titulo }}</h1>
                    <div class="flex items-center gap-4 mt-2">
                        <time datetime="{{ $prompt->created_at }}">
                            {{ $prompt->created_at->format('d/m/Y') }}
                        </time>
                        <span>Por {{ $prompt->user->name }}</span>
                    </div>
                </header>

                <section class="prose max-w-none mt-6">
                    {!! nl2br(e($prompt->contenido)) !!}
                </section>

                <!-- Etiquetas -->
                <footer class="flex flex-wrap gap-2 mt-6">
                    @foreach($prompt->etiquetas as $etiqueta)
                        <a href="{{ route('prompts.index', ['tag' => $etiqueta->slug]) }}"
                           class="badge badge-primary">
                            #{{ $etiqueta->nombre }}
                        </a>
                    @endforeach
                </footer>
            </article>

            <!-- Calificaciones -->
            <section class="mt-8">
                <h3 class="text-xl font-semibold mb-4">Calificaciones</h3>
                @include('prompts.partials.rating-widget')
            </section>

            <!-- Comentarios -->
            <section class="mt-8">
                <h3 class="text-xl font-semibold mb-4">Comentarios</h3>
                @include('prompts.partials.comments')
            </section>
        </div>
    </div>
</x-app-layout>
```

**Elementos Semánticos Clave:**
- `<article>`: Contenido principal del prompt
- `<header>/<footer>`: Metadatos y etiquetas
- `<time>`: Fechas formateadas correctamente
- `<section>`: Bloques funcionales (comentarios, calificaciones)
- `<nav>`: Navegación entre versiones

---

### 7.6.2 CSS - TailwindCSS como Única Fuente

#### ⚡ Zero Custom CSS

PromptVault utiliza **100% TailwindCSS utility classes**, eliminando archivos CSS personalizados.

#### 🎨 Configuración Tailwind

```javascript
// tailwind.config.js
export default {
    content: [
        './resources/**/*.blade.php',
        './resources/**/*.js',
    ],
    theme: {
        extend: {
            colors: {
                primary: {
                    50: '#eff6ff',
                    500: '#3b82f6',
                    900: '#1e3a8a',
                },
            },
            fontFamily: {
                sans: ['Inter', 'sans-serif'],
                mono: ['JetBrains Mono', 'monospace'],
            },
        },
    },
    plugins: [
        require('@tailwindcss/forms'),
    ],
}
```

#### 📱 Responsividad Mobile-First

```html
<!-- Card responsive -->
<div class="
    grid 
    grid-cols-1           /* Mobile: 1 columna */
    sm:grid-cols-2        /* Tablet: 2 columnas */
    lg:grid-cols-3        /* Desktop: 3 columnas */
    gap-4 sm:gap-6
">
    @foreach($prompts as $prompt)
        <x-prompt-card :prompt="$prompt" />
    @endforeach
</div>

<!-- Navegación responsive -->
<nav class="
    flex flex-col        /* Mobile: vertical */
    lg:flex-row          /* Desktop: horizontal */
    gap-4 lg:gap-8
">
    <a href="#" class="nav-link">Dashboard</a>
    <a href="#" class="nav-link">Prompts</a>
</nav>
```

#### 🎭 Estados Interactivos

```html
<!-- Botón con todos los estados -->
<button class="
    px-6 py-3
    bg-gradient-to-r from-blue-500 to-purple-600
    text-white font-semibold rounded-lg
    
    hover:scale-105           /* Hover: escala */
    active:scale-95           /* Click: presión */
    focus:ring-4 focus:ring-blue-300  /* Foco: anillo */
    disabled:opacity-50 disabled:cursor-not-allowed  /* Deshabilitado */
    
    transition-all duration-200
">
    Guardar Prompt
</button>
```

#### 🌙 Dark Mode Support

```html
<!-- Componente con modo oscuro -->
<div class="
    bg-white dark:bg-gray-800
    text-gray-900 dark:text-gray-100
    border border-gray-200 dark:border-gray-700
">
    <!-- Contenido adaptable -->
</div>
```

#### 📊 Utilidades Personalizadas

```css
/* resources/css/app.css - Solo @directives Tailwind */
@import 'tailwindcss';

@layer components {
    .btn-primary {
        @apply px-4 py-2 bg-blue-500 text-white rounded-lg 
               hover:bg-blue-600 transition-colors;
    }
    
    .card {
        @apply bg-white rounded-xl shadow-lg p-6;
    }
}
```

#### 🚀 Optimización de Build

**Antes (CSS tradicional):** ~250KB  
**Después (Tailwind purgado):** ~8KB

```javascript
// vite.config.js - PurgeCSS automático
export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
    build: {
        cssMinify: true,  // Minificación CSS
        rollupOptions: {
            output: {
                manualChunks: {
                    vendor: ['alpinejs'],
                },
            },
        },
    },
});
```

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
