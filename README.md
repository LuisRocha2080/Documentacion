# 📱 GoRide - Aplicación de Transporte

## 6.1 HERRAMIENTAS UTILIZADAS

Este proyecto integra tecnologías modernas para el desarrollo multiplataforma, implementando un stack completo que permite crear una aplicación móvil robusta con un backend escalable. La selección de estas herramientas se basa en la necesidad de mantener un código mantenible, tipado y con alto rendimiento tanto en dispositivos móviles como en el servidor.

### Frontend - Tecnologías Principales

| Herramienta | Versión | Propósito | Documentación |
|-------------|---------|-----------|---------------|
| **React Native** | 0.81.5 | Framework principal para desarrollo móvil multiplataforma | [Docs](https://reactnative.dev/) |
| **Expo** | ~54.0.31 | Plataforma de desarrollo y herramientas para React Native | [Docs](https://docs.expo.dev/) |
| **TypeScript** | ~5.9.2 | Superset de JavaScript con tipado estático | [Docs](https://www.typescriptlang.org/) |
| **Expo Router** | ~6.0.22 | Sistema de navegación basado en archivos | [Docs](https://docs.expo.dev/router/introduction/) |
| **React** | 19.1.0 | Librería base para interfaces de usuario | [Docs](https://react.dev/) |

### Librerías de UI y Funcionalidad

| Librería | Función | Caso de Uso |
|----------|---------|-------------|
| `react-native-maps` | Integración de mapas interactivos | Visualización de rutas y ubicaciones de viajes |
| `react-native-gifted-chat` | Componentes de chat prediseñados | Sistema de mensajería entre usuarios y conductores |
| `@expo/vector-icons` | Iconos vectoriales | Interfaz gráfica consistente |
| `expo-image-picker` | Selección de imágenes | Actualización de foto de perfil |
| `react-native-reanimated` | Animaciones nativas optimizadas | Transiciones fluidas en la UI |
| `@react-native-async-storage/async-storage` | Almacenamiento local persistente | Caché de datos y sesión de usuario |

### Backend - Tecnologías del Servidor

| Herramienta | Versión | Función |
|-------------|---------|---------|
| **Node.js** | LTS | Entorno de ejecución JavaScript del lado del servidor |
| **Express** | ^5.2.1 | Framework web minimalista y flexible |
| **MySQL2** | ^3.16.1 | Cliente MySQL con soporte para Promises |
| **Socket.IO** | ^4.8.3 | Comunicación en tiempo real bidireccional |
| **CORS** | ^2.8.5 | Middleware para permitir peticiones cross-origin |
| **dotenv** | ^17.2.3 | Gestión de variables de entorno |

### Herramientas de Desarrollo

- **Axios** (^1.13.2): Cliente HTTP para peticiones REST
- **Socket.IO Client** (^4.8.3): Cliente para comunicación en tiempo real
- **React Navigation** (^7.1.28): Navegación entre pantallas
- **Expo Haptics**: Feedback táctil en interacciones

### Entorno de Desarrollo

```bash
├── IDE Recomendado: Visual Studio Code / Android Studio
├── Runtime: Node.js 18+ / Expo Go App
├── Base de Datos: MySQL 8.0+
├── Sistema de Control de Versiones: Git
└── Gestor de Paquetes: npm / yarn
```

---

## 6.2 ESTRUCTURA DEL PROYECTO EN ANDROID STUDIO

El proyecto está organizado siguiendo el patrón MVC (Modelo-Vista-Controlador) con una arquitectura modular que separa claramente las responsabilidades. Esta estructura facilita el mantenimiento, escalabilidad y trabajo en equipo, permitiendo que múltiples desarrolladores trabajen en diferentes módulos sin conflictos.

### 📁 Árbol de Directorios Principal

```
GoRide/
│
├── 📱 app/                          # Rutas y pantallas principales (Expo Router)
│   ├── _layout.tsx                 # Layout raíz de la aplicación
│   ├── index.tsx                   # Pantalla de inicio/login
│   ├── registro.tsx                # Pantalla de registro
│   ├── ayuda.tsx                   # Pantalla de ayuda
│   ├── billetera.tsx               # Gestión de pagos
│   ├── chat.tsx                    # Chat individual
│   ├── notificaciones.tsx          # Centro de notificaciones
│   ├── lugares.tsx                 # Lugares guardados
│   └── (tabs)/                     # Navegación por pestañas
│       ├── _layout.tsx             # Layout del tab navigator
│       ├── index.tsx               # Dashboard principal
│       ├── actividad.tsx           # Historial de viajes
│       ├── explorar.tsx            # Exploración de servicios
│       └── perfil.tsx              # Perfil de usuario
│
├── 🎨 src/                          # Código fuente principal (Arquitectura MVC)
│   ├── vistas/                     # 👁️ VISTAS (Capa de Presentación)
│   ├── controladores/              # 🎮 CONTROLADORES (Lógica de Negocio)
│   ├── modelos/                    # 📊 MODELOS (Estructuras de Datos)
│   ├── componentes/                # 🧩 Componentes Reutilizables
│   ├── servicios/                  # 🔌 Servicios Externos (Socket, APIs)
│   ├── context/                    # 🌐 Context API (Estado Global)
│   ├── estilos/                    # 💅 Estilos Globales
│   └── utils/                      # 🛠️ Utilidades y Helpers
│
├── 🖼️ assets/                       # Recursos estáticos
│   ├── images/                     # Imágenes, iconos, splash
│   └── fonts/                      # Fuentes personalizadas
│
├── ⚙️ backend/                      # Servidor Node.js + Express
│   ├── server.js                   # Punto de entrada del servidor
│   ├── migrations/                 # Scripts de migración de BD
│   ├── seeds/                      # Datos de prueba
│   └── package.json                # Dependencias del backend
│
├── 🗄️ database/                     # Configuración de base de datos
│   └── mysql_setup.sql             # Script de inicialización MySQL
│
├── 📚 docs/                         # Documentación del proyecto
│   ├── 01_Aspectos_Generales.md
│   └── 02_Credenciales.md
│
└── ⚙️ Archivos de Configuración
    ├── package.json                # Dependencias frontend
    ├── tsconfig.json               # Configuración TypeScript
    ├── app.json                    # Configuración Expo
    └── expo-env.d.ts               # Tipos globales de Expo
```

### 📊 Distribución de Archivos por Módulo

| Directorio | Total Archivos | Descripción |
|------------|----------------|-------------|
| `/app` | ~15 archivos | Pantallas principales con Expo Router |
| `/src/vistas` | ~12 archivos | Componentes de vista principales |
| `/src/modelos` | ~5 archivos | Interfaces TypeScript |
| `/src/controladores` | ~3 archivos | Lógica de negocio y APIs |
| `/src/componentes` | ~8 archivos | Componentes reutilizables |
| `/src/servicios` | ~2 archivos | Socket.IO y servicios externos |
| `/backend` | ~8 archivos | API REST y lógica del servidor |

### 🔄 Flujo de Navegación (Expo Router)

```
┌─────────────────────────────────────────────┐
│          app/index.tsx (Login)             │
│                                             │
└──────────────────┬──────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────┐
│         app/(tabs)/_layout.tsx              │
│         (Tab Navigation)                    │
│                                             │
│  ┌──────────┬──────────┬──────────┬────────┤
│  │  Home    │Actividad │Explorar  │ Perfil │
│  └──────────┴──────────┴──────────┴────────┘
└─────────────────────────────────────────────┘
                   │
         ┌─────────┼─────────┐
         ▼         ▼         ▼
      Ayuda     Chat    Billetera
```

### 🔗 Integración Frontend-Backend

| Componente | Ubicación | Conexión |
|------------|-----------|----------|
| API REST | `/backend/server.js` | Puerto 3000 |
| Socket.IO | `/src/servicios/SocketService.ts` | WebSocket en puerto 3000 |
| Controlador | `/src/controladores/BaseDeDatos.ts` | Axios HTTP Client |
| Base de Datos | MySQL (Puerto 3306) | Express → MySQL2 |

---

## 6.3 IMPLEMENTACIÓN DE LA ARQUITECTURA MVC

El proyecto implementa el patrón Modelo-Vista-Controlador (MVC) para separar las responsabilidades del código en tres capas distintas. Esta arquitectura permite que los cambios en la interfaz de usuario no afecten la lógica de negocio, y viceversa, facilitando el testing, mantenimiento y escalabilidad del sistema.

### 🏗️ Diagrama de Arquitectura MVC

```
┌─────────────────────────────────────────────────────────────┐
│                    CAPA DE VISTA (View)                     │
│                                                               │
│  📱 /src/vistas/                    📱 /app/                 │
│  ├── DashboardVista.tsx  ────────► ├── (tabs)/index.tsx     │
│  ├── PerfilVista.tsx     ────────► ├── (tabs)/perfil.tsx    │
│  ├── ActividadVista.tsx  ────────► ├── (tabs)/actividad.tsx │
│  └── ChatVista.tsx       ────────► └── chat.tsx             │
│                                                               │
│          ▲                              ▲                     │
│          │ Renderiza UI                 │ Rutas              │
│          │                              │                     │
└──────────┼──────────────────────────────┼─────────────────────┘
           │                              │
           │                              │
┌──────────┼──────────────────────────────┼─────────────────────┐
│          │        CAPA CONTROLADOR (Controller)               │
│          │                                                     │
│  🎮 /src/controladores/              🌐 /src/context/        │
│  ├── BaseDeDatos.ts ◄──────────────► ├── AuthContext.tsx    │
│  │  ├── login()                      │  ├── login()          │
│  │  ├── obtenerUsuario()             │  ├── logout()         │
│  │  ├── crearViaje()                 │  └── updateUser()     │
│  │  └── obtenerViajes()                                      │
│  │                                                            │
│  └── 🔌 /src/servicios/                                      │
│      └── SocketService.ts (Tiempo Real)                      │
│                                                               │
│          │                              │                     │
│          │ Procesa lógica               │ Gestiona estado    │
│          │                              │                     │
└──────────┼──────────────────────────────┼─────────────────────┘
           │                              │
           ▼                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    CAPA MODELO (Model)                       │
│                                                               │
│  📊 /src/modelos/                 💾 /backend/               │
│  ├── Usuario.ts                  ├── server.js               │
│  ├── Viaje.ts                    └── MySQL Database          │
│  ├── Conductor.ts                    ├── usuarios            │
│  ├── Pago.ts                         ├── viajes              │
│  └── Producto.ts                     ├── conductores         │
│                                       └── pagos               │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### 📊 MODELO (Model) - Estructuras de Datos

Los modelos definen las interfaces TypeScript que representan las entidades del sistema y garantizan el tipado seguro en toda la aplicación.

#### Archivos y Responsabilidades

| Archivo | Entidad | Propiedades Principales | Ubicación |
|---------|---------|------------------------|-----------|
| `Usuario.ts` | Usuario del sistema | `id`, `nombre`, `email`, `telefono`, `es_conductor` | [src/modelos/](src/modelos/Usuario.ts) |
| `Viaje.ts` | Viaje/solicitud | `id`, `usuario_id`, `conductor_id`, `origen`, `destino`, `estado` | [src/modelos/](src/modelos/Viaje.ts) |
| `Conductor.ts` | Perfil de conductor | `id`, `usuario_id`, `vehiculo_tipo`, `placa`, `rating` | [src/modelos/](src/modelos/Conductor.ts) |
| `Pago.ts` | Transacciones | `id`, `viaje_id`, `monto`, `metodo`, `estado` | [src/modelos/](src/modelos/Pago.ts) |
| `Producto.ts` | Servicios disponibles | `id`, `nombre`, `categoria`, `precio_base` | [src/modelos/](src/modelos/Producto.ts) |

#### Ejemplo de Implementación: Usuario.ts

```typescript
export interface Usuario {
  id: number;
  nombre: string;
  email: string;
  telefono?: string;
  foto_perfil?: string;
  es_conductor: boolean;
  created_at?: string;
}
```

**Características:**
- ✅ Tipado estático con TypeScript
- ✅ Propiedades opcionales con `?`
- ✅ Exportación como interfaz reutilizable
- ✅ Validación en tiempo de compilación

### 🎮 CONTROLADOR (Controller) - Lógica de Negocio

Los controladores gestionan las operaciones CRUD, comunicación con el backend y lógica de negocio de la aplicación.

#### BaseDeDatos.ts - Controlador Principal

| Método | Parámetros | Retorno | Función |
|--------|-----------|---------|---------|
| `login()` | `email`, `password` | `Promise<Usuario>` | Autenticación de usuario |
| `registrarUsuario()` | `datos: Usuario` | `Promise<Usuario>` | Registro de nuevo usuario |
| `obtenerUsuario()` | `id: number` | `Promise<Usuario>` | Obtener datos de usuario |
| `crearViaje()` | `datos: Viaje` | `Promise<Viaje>` | Solicitar nuevo viaje |
| `obtenerViajes()` | `usuario_id: number` | `Promise<Viaje[]>` | Historial de viajes |
| `actualizarEstadoViaje()` | `viaje_id`, `estado` | `Promise<void>` | Cambiar estado del viaje |
| `obtenerConductoresDisponibles()` | `ubicacion` | `Promise<Conductor[]>` | Buscar conductores cercanos |

#### Estructura del Controlador

```typescript
export const BaseDeDatos = {
  // Autenticación
  async login(email: string, password: string) {
    const response = await fetchWithTimeout(`${API_URL}/login`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    });
    return response.json();
  },

  // CRUD de Viajes
  async crearViaje(datos: Viaje) {
    const response = await fetchWithTimeout(`${API_URL}/viajes`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(datos)
    });
    return response.json();
  }
}
```

#### Servicios Adicionales

| Servicio | Archivo | Propósito |
|----------|---------|-----------|
| **Socket.IO** | `SocketService.ts` | Comunicación en tiempo real (ubicación, mensajes) |
| **Context API** | `AuthContext.tsx` | Estado global de autenticación |
| **Pricing** | `pricing.ts` | Cálculo de tarifas de viaje |
| **Simulation** | `simulation.ts` | Simulación de rutas y movimiento |

### 👁️ VISTA (View) - Capa de Presentación

Las vistas son componentes React Native que presentan la información al usuario y capturan sus interacciones.

#### Vistas Principales del Sistema

| Vista | Archivo | Responsabilidad | Controladores Usados |
|-------|---------|-----------------|---------------------|
| **Dashboard** | `DashboardVista.tsx` | Pantalla principal, solicitud de viajes | `BaseDeDatos`, `SocketService` |
| **Perfil** | `PerfilVista.tsx` | Gestión de datos del usuario | `BaseDeDatos`, `AuthContext` |
| **Actividad** | `ActividadVista.tsx` | Historial de viajes realizados | `BaseDeDatos` |
| **Chat** | `ChatVista.tsx` | Mensajería en tiempo real | `SocketService` |
| **Conductor** | `ConductorVista.tsx` | Panel del conductor (aceptar viajes) | `BaseDeDatos`, `SocketService` |
| **Billetera** | `BilleteraVista.tsx` | Gestión de pagos y saldo | `BaseDeDatos` |
| **Ayuda** | `AyudaVista.tsx` | Centro de ayuda y soporte | - |

#### Componentes Reutilizables

| Componente | Archivo | Función | Props Clave |
|------------|---------|---------|-------------|
| **Mapa** | `Mapa.tsx` | Renderizado de mapas interactivos | `ubicacion`, `destino`, `ruta` |
| **BuscadorDestino** | `BuscadorDestino.tsx` | Búsqueda y selección de ubicaciones | `onDestinoSeleccionado` |
| **SelectorVehiculo** | `SelectorVehiculo.tsx` | Selección de tipo de vehículo | `vehiculos`, `onSeleccionar` |
| **PanelViaje** | `PanelViaje.tsx` | Información del viaje en curso | `viaje`, `conductor` |
| **CalificacionViaje** | `CalificacionViaje.tsx` | Sistema de valoración post-viaje | `viaje`, `onCalificar` |
| **ResumenViaje** | `ResumenViaje.tsx` | Detalles finales del viaje | `viaje`, `costo` |

#### Ejemplo de Implementación: DashboardVista.tsx

```typescript
export default function DashboardVista() {
  const { user } = useAuth();  // Context (Estado Global)
  const [fase, setFase] = useState<"inicio" | "busqueda" | "en_viaje">("inicio");
  
  const solicitarViaje = async () => {
    const viaje = await BaseDeDatos.crearViaje({
      usuario_id: user.id,
      origen: origenSeleccionado,
      destino: destinoSeleccionado,
      vehiculo_tipo: vehiculoSeleccionado
    });
    
    SocketService.emit('solicitar_viaje', viaje);
    setFase("solicitando");
  };
  
  return (
    <View>
      <Mapa ubicacion={ubicacion} destino={destino} />
      <BuscadorDestino onDestinoSeleccionado={setDestino} />
      <SelectorVehiculo onSeleccionar={solicitarViaje} />
    </View>
  );
}
```

### 🔄 Flujo de Datos MVC en Acción

#### Ejemplo: Solicitar un Viaje

```
1. Usuario interactúa con la Vista
   └─► DashboardVista.tsx: Presiona "Solicitar Viaje"

2. Vista invoca al Controlador
   └─► BaseDeDatos.crearViaje({ origen, destino, usuario_id })

3. Controlador procesa y comunica con el Backend
   └─► POST /api/viajes → Express Server

4. Backend interactúa con el Modelo
   └─► MySQL INSERT INTO viajes (...)

5. Respuesta regresa al Controlador
   └─► Viaje creado con ID asignado

6. Controlador actualiza la Vista
   └─► setViaje(nuevoViaje), setFase("en_viaje")

7. Socket.IO notifica en tiempo real
   └─► SocketService.emit('nuevo_viaje', viaje)
   └─► Conductores reciben notificación
```

### 📈 Ventajas de la Arquitectura MVC Implementada

| Ventaja | Descripción | Impacto |
|---------|-------------|---------|
| **Separación de Responsabilidades** | Cada capa tiene una función específica | Código más limpio y mantenible |
| **Reutilización de Código** | Controladores y modelos compartidos | Menos duplicación |
| **Facilidad de Testing** | Cada capa se puede probar independientemente | Mayor cobertura de tests |
| **Escalabilidad** | Nuevas funcionalidades sin refactorización masiva | Desarrollo más rápido |
| **Trabajo en Equipo** | Múltiples devs trabajando en paralelo | Menos conflictos de merge |
| **Tipado Seguro** | TypeScript en toda la arquitectura | Menos bugs en producción |

### 🧪 Testing por Capas

```typescript
// Test del Modelo
test('Usuario.ts debe tener propiedades requeridas', () => {
  const usuario: Usuario = {
    id: 1,
    nombre: "Juan",
    email: "juan@test.com",
    es_conductor: false
  };
  expect(usuario.id).toBeDefined();
});

// Test del Controlador
test('BaseDeDatos.login debe retornar un usuario', async () => {
  const usuario = await BaseDeDatos.login('test@mail.com', 'password');
  expect(usuario).toHaveProperty('id');
});

// Test de la Vista
test('DashboardVista debe renderizar el mapa', () => {
  const { getByTestId } = render(<DashboardVista />);
  expect(getByTestId('mapa')).toBeTruthy();
});
```

---

## 🚀 Ejecución del Proyecto

### Requisitos Previos

- Node.js 18+
- MySQL 8.0+
- Expo Go App (dispositivo móvil)
- Android Studio (opcional para emulador)

### Instalación

```bash
# 1. Clonar repositorio
git clone <url-repositorio>
cd GoRide

# 2. Instalar dependencias frontend
npm install

# 3. Instalar dependencias backend
cd backend
npm install
cd ..

# 4. Configurar base de datos
mysql -u root -p < database/mysql_setup.sql

# 5. Configurar variables de entorno
# Crear archivo .env en /backend con:
# DB_HOST=localhost
# DB_USER=root
# DB_PASSWORD=tu_password
# DB_NAME=goride_db
```

### Ejecutar Aplicación

```bash
# Terminal 1: Iniciar backend
npm run start --prefix backend

# Terminal 2: Iniciar Expo
npm start

# Escanear código QR con Expo Go
```

### Tareas Disponibles

| Comando | Descripción |
|---------|-------------|
| `npm start` | Inicia el servidor de desarrollo Expo |
| `npm run android` | Abre en emulador Android |
| `npm run ios` | Abre en simulador iOS |
| `npm run migrate` | Ejecuta migraciones de BD |
| `npm run seed` | Carga datos de prueba |

---

## 📄 Licencia

Este proyecto es parte de un trabajo académico y está disponible bajo licencia MIT.

## 👥 Autores

Desarrollado como proyecto final de la materia de Desarrollo de Aplicaciones Móviles.

---

**Última actualización:** Enero 2026
