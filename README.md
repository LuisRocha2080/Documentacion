# 🗂️ PromptVault

Sistema web de gestión de prompts con integración multi-provider de IA.

---

## 7.4 Proceso del Prototipado Inicial

El diseño de PromptVault siguió una metodología **Figma-first**, validando la experiencia de usuario antes de escribir código.

### 📐 Flujo de Trabajo

```
Investigación → Wireframes Figma → UI Design → Prototipo Interactivo → Desarrollo
```

| Fase | Herramienta | Entregable |
|------|-------------|------------|
| **Wireframing** | Figma (baja fidelidad) | Estructura de páginas y flujos |
| **UI Design** | Figma (alta fidelidad) | Sistema de diseño completo |
| **Prototyping** | Figma Interactive | Navegación con transiciones |
| **Handoff** | Figma Dev Mode | Especificaciones CSS exactas |

### ✅ Validación Pre-Código

**Pantallas prototipadas:**
- Dashboard con métricas
- CRUD de prompts (crear/editar/listar/detalle)
- Sistema de calificación y comentarios
- Chat con selección de provider IA
- Panel de administración

**Elementos validados:**
- Flujos de navegación completos
- Estados de formularios (vacío/error/éxito)
- Comportamiento de modales y dropdowns
- Responsive breakpoints (mobile/tablet/desktop)

---

## 7.5 Elementos Visuales del Sistema

### 🎨 Paleta de Colores

```css
/* Primarios */
--blue-500: #3B82F6;     /* Acciones principales */
--purple-600: #9333EA;   /* Énfasis y gradientes */
--gray-900: #111827;     /* Textos */

/* Semánticos */
--success: #10B981;      /* Éxito */
--warning: #F59E0B;      /* Alertas */
--error: #EF4444;        /* Errores */
```

**Uso:**
- Botones CTA: Gradiente `blue-500 → purple-600`
- Navegación: `gray-800` con hover `blue-500`

### 🔤 Tipografía

| Elemento | Fuente | Tamaño | Peso |
|----------|--------|--------|------|
| Headings | Inter | 2xl-4xl | 700 |
| Body | Inter | base | 400 |
| Code | JetBrains Mono | sm | 500 |

### 🔲 Iconografía

**Sistema:** Heroicons 2.0

**Tamaños:**
- Botones: 20px (w-5 h-5)
- Navegación: 24px (w-6 h-6)
- Hero: 32px (w-8 h-8)

### 📐 Espaciado

- **Padding componentes**: `p-4`, `p-6`
- **Margen secciones**: `my-8`, `my-12`
- **Gap grids**: `gap-4`, `gap-6`

### 🎭 Componentes Reutilizables

| Componente | Archivo |
|-----------|---------|
| `x-form-label` | `components/form-label.blade.php` |
| `x-form-select` | `components/form-select.blade.php` |
| `x-button` | `components/button.blade.php` |
| `x-modal` | `components/modal.blade.php` |
| `x-alert` | `components/alert.blade.php` |

---

## 7.6 Traducción del Prototipo a Código

### 🔄 Proceso de Conversión

```
Figma Design → Exportar Assets → Blade Components → TailwindCSS → Alpine.js
```

**Ejemplo práctico:**

**🎨 En Figma:**
- Card con padding 24px, radius 12px, shadow suave

**💻 En Código:**
```html
<div class="bg-white rounded-xl shadow-lg p-6">
    <!-- Contenido -->
</div>
```

**🧩 Componente Blade:**
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

## 7.6.1 HTML - Estructura Semántica

### 🏗️ Jerarquía de Vistas Blade

```
layouts/
├── app.blade.php        # Layout maestro
├── admin.blade.php      # Admin con sidebar
└── guest.blade.php      # Público

prompts/
├── index.blade.php      # Listado
├── show.blade.php       # Detalle
├── create.blade.php     # Crear
└── edit.blade.php       # Editar
```

### 📄 Ejemplo: Vista de Detalle

```html
<x-app-layout>
    <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
            
            <!-- Contenido principal -->
            <article class="bg-white rounded-lg shadow-md p-8">
                <header>
                    <h1 class="text-3xl font-bold">{{ $prompt->titulo }}</h1>
                    <time datetime="{{ $prompt->created_at }}">
                        {{ $prompt->created_at->format('d/m/Y') }}
                    </time>
                </header>

                <section class="prose mt-6">
                    {!! nl2br(e($prompt->contenido)) !!}
                </section>

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

        </div>
    </div>
</x-app-layout>
```

**Elementos semánticos clave:**
- `<article>`: Contenido principal
- `<header>/<footer>`: Metadatos
- `<time>`: Fechas con atributo `datetime`
- `<section>`: Bloques funcionales

---

## 7.6.2 CSS - TailwindCSS

### ⚡ 100% Utility-First

PromptVault utiliza **exclusivamente TailwindCSS**, sin archivos CSS personalizados.

### 🎨 Configuración Base

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

### 📱 Responsividad Mobile-First

```html
<!-- Grid responsive -->
<div class="
    grid 
    grid-cols-1          /* Mobile: 1 columna */
    sm:grid-cols-2       /* Tablet: 2 columnas */
    lg:grid-cols-3       /* Desktop: 3 columnas */
    gap-4 sm:gap-6
">
    @foreach($prompts as $prompt)
        <x-prompt-card :prompt="$prompt" />
    @endforeach
</div>
```

### 🎭 Estados Interactivos

```html
<button class="
    px-6 py-3
    bg-gradient-to-r from-blue-500 to-purple-600
    text-white font-semibold rounded-lg
    
    hover:scale-105          /* Hover */
    active:scale-95          /* Click */
    focus:ring-4 focus:ring-blue-300    /* Foco */
    disabled:opacity-50 disabled:cursor-not-allowed
    
    transition-all duration-200
">
    Guardar
</button>
```

### 🌙 Soporte Dark Mode

```html
<div class="
    bg-white dark:bg-gray-800
    text-gray-900 dark:text-gray-100
    border border-gray-200 dark:border-gray-700
">
    Contenido adaptable
</div>
```

### 📊 Optimización de Build

**Resultado:**
- CSS tradicional: ~250KB
- TailwindCSS purgado: **~8KB**

```javascript
// vite.config.js
export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
    build: {
        cssMinify: true,
    },
});
```

---

## 📦 Instalación

```bash
git clone https://github.com/tu-usuario/promptvault.git
cd promptvault
composer setup
cp .env.example .env
php artisan migrate --seed
npm install && npm run dev
php artisan serve
```
