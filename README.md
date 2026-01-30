# 📱 GoRide - Documentación Técnica

# CAPÍTULO VII
# ARQUITECTURA Y SEGURIDAD DEL SISTEMA

## 7.1 ARQUITECTURA GENERAL DEL SISTEMA

La arquitectura de GoRide implementa un modelo cliente-servidor distribuido de tres capas, combinando tecnologías web modernas con un enfoque de diseño orientado a servicios. Esta arquitectura separa claramente la lógica de presentación, negocio y datos, permitiendo escalabilidad horizontal, mantenimiento independiente de componentes y alta disponibilidad del sistema.

### 🏗️ Modelo de Arquitectura de Tres Capas

```
┌─────────────────────────────────────────────────────────────────┐
│                     CAPA 1: PRESENTACIÓN                        │
│                       (Cliente Móvil)                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  📱 React Native App (Expo)                                     │
│  ┌────────────────────────────────────────────────────────┐    │
│  │                                                         │    │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐ │    │
│  │  │   VISTAS     │  │ COMPONENTES  │  │   CONTEXT    │ │    │
│  │  │ (UI Layer)   │  │  (Shared)    │  │  (Estado)    │ │    │
│  │  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘ │    │
│  │         │                 │                  │          │    │
│  │         └─────────────────┼──────────────────┘          │    │
│  │                           │                             │    │
│  │                  ┌────────▼────────┐                    │    │
│  │                  │  CONTROLADORES  │                    │    │
│  │                  │  BaseDeDatos.ts │                    │    │
│  │                  │  (HTTP Client)  │                    │    │
│  │                  └────────┬────────┘                    │    │
│  │                           │                             │    │
│  └───────────────────────────┼─────────────────────────────┘    │
│                              │                                  │
│                              │ REST API + WebSocket             │
│                              │ (HTTP/HTTPS + Socket.IO)         │
└──────────────────────────────┼──────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│                     CAPA 2: LÓGICA DE NEGOCIO                   │
│                        (Servidor Backend)                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  🖥️ Node.js + Express Server                                    │
│  ┌────────────────────────────────────────────────────────┐    │
│  │                                                         │    │
│  │  ┌──────────────────────────────────────────────────┐  │    │
│  │  │           REST API ENDPOINTS                      │  │    │
│  │  │  • /api/login          • /api/viajes            │  │    │
│  │  │  • /api/register       • /api/conductores       │  │    │
│  │  │  • /api/usuarios       • /api/metodos_pago      │  │    │
│  │  │  • /api/mensajes       • /api/notificaciones    │  │    │
│  │  └──────────────────────────────────────────────────┘  │    │
│  │                                                         │    │
│  │  ┌──────────────────────────────────────────────────┐  │    │
│  │  │         MIDDLEWARE & SERVICIOS                    │  │    │
│  │  │  • CORS Handler       • JSON Parser              │  │    │
│  │  │  • Error Handler      • Validaciones             │  │    │
│  │  │  • Socket.IO Server   • Logging                  │  │    │
│  │  └──────────────────────────────────────────────────┘  │    │
│  │                                                         │    │
│  │  ┌──────────────────────────────────────────────────┐  │    │
│  │  │       LÓGICA DE NEGOCIO                          │  │    │
│  │  │  • Asignación de conductores (Haversine)         │  │    │
│  │  │  • Cálculo de tarifas                            │  │    │
│  │  │  • Gestión de estados de viajes                  │  │    │
│  │  │  • Sistema de calificaciones                     │  │    │
│  │  └──────────────────────────────────────────────────┘  │    │
│  │                           │                             │    │
│  └───────────────────────────┼─────────────────────────────┘    │
│                              │                                  │
│                              │ MySQL Driver (mysql2)            │
│                              │                                  │
└──────────────────────────────┼──────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│                     CAPA 3: DATOS                               │
│                   (Base de Datos Relacional)                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  💾 MySQL Database Server                                       │
│  ┌────────────────────────────────────────────────────────┐    │
│  │                                                         │    │
│  │  Tablas Principales:                                    │    │
│  │  ├── usuarios            (Datos de usuarios)           │    │
│  │  ├── conductores         (Perfiles de conductores)     │    │
│  │  ├── viajes              (Solicitudes y viajes)        │    │
│  │  ├── mensajes            (Chat en tiempo real)         │    │
│  │  ├── calificaciones      (Ratings de viajes)           │    │
│  │  ├── metodos_pago        (Medios de pago)              │    │
│  │  ├── notificaciones      (Alertas del sistema)         │    │
│  │  ├── lugares_guardados   (Direcciones favoritas)       │    │
│  │  ├── vehiculos           (Datos de vehículos)          │    │
│  │  ├── sesiones            (Control de sesiones)         │    │
│  │  └── soporte_tickets     (Tickets de ayuda)            │    │
│  │                                                         │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 📊 Componentes Principales del Sistema

| Componente | Capa | Tecnología | Puerto | Función Principal |
|------------|------|------------|--------|-------------------|
| **React Native App** | Presentación | Expo + TypeScript | N/A | Interfaz de usuario móvil |
| **BaseDeDatos Controller** | Presentación | TypeScript + Fetch API | N/A | Cliente HTTP y gateway |
| **Express Server** | Negocio | Node.js + Express | 3000 | API REST y lógica |
| **Socket.IO Server** | Negocio | Socket.IO | 3000 | Comunicación en tiempo real |
| **MySQL Database** | Datos | MySQL 8.0+ | 3306 | Persistencia de datos |
| **AsyncStorage** | Presentación | React Native | N/A | Almacenamiento local |

### 🔄 Flujo de Comunicación Cliente-Servidor

#### 1. Arquitectura de Comunicación REST

```
┌────────────────────────────────────────────────────────────────┐
│                    FLUJO DE PETICIÓN HTTP                      │
└────────────────────────────────────────────────────────────────┘

Vista (React Component)
    │
    │ Llamada a función
    ▼
AuthContext / Hook
    │
    │ Invoca método
    ▼
BaseDeDatos.ts (Controlador)
    │
    │ HTTP Request
    │ ┌──────────────────────────────────────────┐
    │ │ fetch(`http://IP:3000/api/endpoint`, {  │
    │ │   method: 'POST',                        │
    │ │   headers: { 'Content-Type': 'json' },   │
    │ │   body: JSON.stringify(data)             │
    │ │ })                                       │
    │ └──────────────────────────────────────────┘
    │
    ▼
═══════════════════════════════════════════════════════════════
        INTERNET / RED LOCAL (Wi-Fi / Cellular)
═══════════════════════════════════════════════════════════════
    │
    ▼
Express Middleware
    ├─► CORS Handler      (Permitir cross-origin)
    ├─► JSON Parser       (Parsear body)
    ├─► Error Handler     (Capturar errores)
    └─► Router            (Enrutar petición)
        │
        ▼
    Endpoint Handler (server.js)
        │
        │ Query SQL
        ▼
    MySQL Database
        │
        │ Resultado
        ▼
    JSON Response
        │
═══════════════════════════════════════════════════════════════
        INTERNET / RED LOCAL (Wi-Fi / Cellular)
═══════════════════════════════════════════════════════════════
    │
    ▼
BaseDeDatos.ts (Controlador)
    │
    │ return response.json()
    ▼
AuthContext / Hook
    │
    │ Actualizar estado
    ▼
Vista (React Component)
    │
    │ Re-render UI
    ▼
Usuario ve resultado
```

#### 2. Arquitectura de Comunicación en Tiempo Real (WebSocket)

```
┌────────────────────────────────────────────────────────────────┐
│              COMUNICACIÓN BIDIRECCIONAL SOCKET.IO              │
└────────────────────────────────────────────────────────────────┘

Cliente (SocketService.ts)                 Servidor (Socket.IO)
    │                                             │
    │ socket.emit('evento', datos)                │
    ├─────────────────────────────────────────────►
    │                                             │
    │                                    ┌────────▼────────┐
    │                                    │ Procesar evento │
    │                                    │ Actualizar BD   │
    │                                    └────────┬────────┘
    │                                             │
    │                socket.broadcast()           │
    ◄─────────────────────────────────────────────┤
    │                                             │
    ▼                                             ▼
Callback ejecuta                        Notifica a otros clientes
UI actualiza en tiempo real             conectados al mismo room
```

### 🌐 Configuración de Red y Endpoints

#### Configuración Dinámica del API_URL

```typescript
// 📍 src/controladores/BaseDeDatos.ts

const getApiUrl = () => {
  // Opción 1: IP Manual (Producción / Wi-Fi)
  const manualIP = "172.25.3.48";  // Cambiar según red
  return `http://${manualIP}:3000/api`;

  // Opción 2: Detección Dinámica (Expo Go)
  // const debuggerHost = Constants.expoConfig?.hostUri;
  // const localhost = debuggerHost?.split(":")[0];
  // return `http://${localhost}:3000/api`;

  // Opción 3: Emulador Android
  // return "http://10.0.2.2:3000/api";
};

export const API_URL = getApiUrl();
```

#### Tabla de Configuraciones por Entorno

| Entorno | IP/Host | Puerto | URL Completa | Uso |
|---------|---------|--------|--------------|-----|
| **Desarrollo Local (Web)** | localhost | 3000 | `http://localhost:3000/api` | Pruebas en navegador |
| **Expo Go (Dispositivo Físico)** | IP de tu PC | 3000 | `http://192.168.X.X:3000/api` | Testing en móvil real |
| **Emulador Android** | 10.0.2.2 | 3000 | `http://10.0.2.2:3000/api` | Emulador Android Studio |
| **Simulador iOS** | localhost | 3000 | `http://localhost:3000/api` | Simulador Xcode |
| **Producción** | dominio.com | 443 | `https://api.dominio.com` | Servidor en nube |

### 🔒 Capas de Seguridad

```
┌─────────────────────────────────────────────────────────────────┐
│                   CAPAS DE SEGURIDAD DEL SISTEMA                │
└─────────────────────────────────────────────────────────────────┘

1. SEGURIDAD EN CLIENTE (Frontend)
   ├─► Validación de entrada de usuario
   ├─► AsyncStorage (almacenamiento local)
   ├─► HTTPS en producción
   └─► No almacenar contraseñas en texto plano

2. SEGURIDAD EN TRANSPORTE (Red)
   ├─► CORS configurado (solo orígenes permitidos)
   ├─► HTTPS/TLS en producción
   ├─► Timeout en peticiones (15 segundos)
   └─► Validación de tamaño de payload (50MB)

3. SEGURIDAD EN SERVIDOR (Backend)
   ├─► Validación de datos de entrada
   ├─► SQL Parameterizado (prevenir inyección)
   ├─► Middleware de autenticación (TODO: JWT)
   ├─► Rate limiting (TODO)
   └─► Logging de actividades

4. SEGURIDAD EN DATOS (Base de Datos)
   ├─► Credenciales en variables de entorno
   ├─► Claves foráneas y restricciones
   ├─► Encriptación de contraseñas (TODO: bcrypt)
   ├─► Backups automáticos
   └─► Acceso restringido por usuario
```

### 📦 Despliegue y Escalabilidad

#### Arquitectura de Despliegue Recomendada

| Componente | Servidor | Replicación | Auto-scaling |
|------------|----------|-------------|--------------|
| **Frontend (Expo)** | CDN (Web) / App Store | N/A | N/A |
| **API Express** | AWS EC2 / DigitalOcean | Load Balancer + 2-N instancias | ✅ Horizontal |
| **MySQL** | AWS RDS / Cloud SQL | Master-Slave Replication | ✅ Vertical |
| **Socket.IO** | Socket cluster | Redis Adapter para multi-nodos | ✅ Horizontal |
| **Assets (Imágenes)** | S3 / Cloudinary | CDN Global | N/A |

#### Escalabilidad Horizontal

```
                    Load Balancer (Nginx)
                            │
           ┌────────────────┼────────────────┐
           │                │                │
           ▼                ▼                ▼
    ┌──────────┐     ┌──────────┐     ┌──────────┐
    │ Express  │     │ Express  │     │ Express  │
    │ Server 1 │     │ Server 2 │     │ Server N │
    └────┬─────┘     └────┬─────┘     └────┬─────┘
         │                │                │
         └────────────────┼────────────────┘
                          │
                    ┌─────▼─────┐
                    │   MySQL   │
                    │  Cluster  │
                    └───────────┘
```

### 🎯 Ventajas de la Arquitectura Implementada

| Ventaja | Descripción | Beneficio |
|---------|-------------|-----------|
| **Separación de Responsabilidades** | Cada capa tiene funciones específicas | Mantenimiento independiente |
| **Escalabilidad** | Capas pueden escalar por separado | Optimización de recursos |
| **Flexibilidad** | Frontend puede cambiar sin afectar backend | Desarrollo ágil |
| **Reutilización** | API REST puede servir múltiples clientes | Web, iOS, Android con mismo backend |
| **Testing** | Cada capa se prueba independientemente | Mayor cobertura de tests |
| **Seguridad** | Capas de seguridad en cada nivel | Defensa en profundidad |

---

## 7.2 CONTROLADOR COMO NÚCLEO DEL SISTEMA

El archivo `BaseDeDatos.ts` actúa como el controlador central del sistema, funcionando como un gateway único entre la capa de presentación y el backend. Este patrón de diseño (API Gateway Pattern) centraliza toda la comunicación HTTP, proporciona una interfaz consistente para las vistas y encapsula la lógica de comunicación con el servidor.

### 🎮 Arquitectura del Controlador

```
┌─────────────────────────────────────────────────────────────────┐
│               BASEDEDATOS.TS - CONTROLADOR CENTRAL              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │          CONFIGURACIÓN Y UTILIDADES                     │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • getApiUrl()        → Determina URL del servidor      │    │
│  │ • fetchWithTimeout() → Peticiones con timeout          │    │
│  │ • API_URL            → Constante exportada             │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │               MÓDULO DE AUTENTICACIÓN                   │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • login()            → POST /api/login                 │    │
│  │ • register()         → POST /api/register              │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │               MÓDULO DE USUARIOS                        │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • obtenerUsuario()      → GET /api/usuarios/:id        │    │
│  │ • actualizarPerfil()    → PUT /api/usuarios/:id        │    │
│  │ • actualizarPassword()  → PUT /api/usuarios/:id/pwd    │    │
│  │ • actualizarFoto()      → PUT /api/usuarios/:id/foto   │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │               MÓDULO DE VIAJES                          │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • solicitarViaje()         → POST /api/viajes/solicitar│    │
│  │ • obtenerHistorial()       → GET /api/viajes/historial │    │
│  │ • obtenerViajesPendientes()→ GET /api/viajes/pendientes│    │
│  │ • aceptarViaje()           → POST /api/viajes/aceptar  │    │
│  │ • completarViaje()         → POST /api/viajes/completar│    │
│  │ • cancelarViaje()          → PUT /api/viajes/:id/cancel│    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │               MÓDULO DE CONDUCTORES                     │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • actualizarEstadoConductor() → POST /api/conductores  │    │
│  │ • obtenerVehiculos()          → GET /api/vehiculos/:id │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │               MÓDULO DE MÉTODOS DE PAGO                 │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • obtenerMetodosPago()  → GET /api/metodos_pago/:id    │    │
│  │ • agregarMetodoPago()   → POST /api/metodos_pago       │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │               MÓDULO DE CHAT                            │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • obtenerMensajes()  → GET /api/mensajes/:viaje_id     │    │
│  │ • enviarMensaje()    → POST /api/mensajes              │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │               MÓDULO DE SOPORTE                         │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • obtenerNotificaciones() → GET /api/notificaciones/:id│    │
│  │ • obtenerTickets()        → GET /api/soporte/:id       │    │
│  │ • crearTicket()           → POST /api/soporte          │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌────────────────────────────────────────────────────────┐    │
│  │               MÓDULO DE LUGARES                         │    │
│  ├────────────────────────────────────────────────────────┤    │
│  │ • obtenerLugares()  → GET /api/lugares/:usuario_id     │    │
│  │ • guardarLugar()    → POST /api/lugares                │    │
│  └────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 📊 Métodos del Controlador por Categoría

#### Tabla de Operaciones Disponibles

| Categoría | Total Métodos | Endpoints | Tipo de Operaciones |
|-----------|---------------|-----------|---------------------|
| **Autenticación** | 2 | `/api/login`, `/api/register` | POST |
| **Usuarios** | 4 | `/api/usuarios/*` | GET, PUT |
| **Viajes** | 6 | `/api/viajes/*` | GET, POST, PUT |
| **Conductores** | 2 | `/api/conductores/*` | GET, POST |
| **Métodos de Pago** | 2 | `/api/metodos_pago/*` | GET, POST |
| **Chat/Mensajes** | 2 | `/api/mensajes/*` | GET, POST |
| **Notificaciones** | 1 | `/api/notificaciones/*` | GET |
| **Soporte** | 2 | `/api/soporte/*` | GET, POST |
| **Lugares** | 2 | `/api/lugares/*` | GET, POST |
| **Total** | **23 métodos** | **9 recursos** | GET, POST, PUT, DELETE |

### 🔧 Funcionalidades Clave del Controlador

#### 1. Gestión de Timeout

```typescript
// 📍 Ubicación: src/controladores/BaseDeDatos.ts

const fetchWithTimeout = async (
  url: string,
  options: any = {},
  timeout = 15000,  // 15 segundos
) => {
  const controller = new AbortController();
  const id = setTimeout(() => controller.abort(), timeout);
  
  try {
    const response = await fetch(url, {
      ...options,
      signal: controller.signal,
    });
    clearTimeout(id);
    return response;
  } catch (error) {
    clearTimeout(id);
    throw error;
  }
};
```

**Beneficios:**
- ✅ Evita peticiones que cuelgan indefinidamente
- ✅ Mejora la experiencia de usuario
- ✅ Libera recursos del dispositivo
- ✅ Permite mostrar mensajes de error apropiados

#### 2. Manejo de Errores Centralizado

```typescript
// Patrón consistente en todos los métodos
login: async (email: string, password: string) => {
  try {
    const response = await fetchWithTimeout(`${API_URL}/login`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ email, password }),
    });
    return await response.json();
  } catch (error) {
    console.error("Login Error:", error);
    return { success: false, message: "Error de conexión" };
  }
}
```

**Ventajas:**
| Aspecto | Implementación | Resultado |
|---------|----------------|-----------|
| **Consistencia** | Todos los métodos tienen try-catch | Código predecible |
| **Logging** | console.error en cada error | Debugging facilitado |
| **Respuesta Uniforme** | Siempre retorna `{success, message}` | UI coherente |
| **No Crashes** | Errores capturados | App estable |

#### 3. Configuración Dinámica de API_URL

```typescript
const getApiUrl = () => {
  // Configuración manual para Wi-Fi
  const manualIP = "172.25.3.48";
  return `http://${manualIP}:3000/api`;
  
  // Otras opciones comentadas:
  // - Detección automática con Expo
  // - IP para emulador Android
  // - Localhost para web
};

export const API_URL = getApiUrl();
console.log("🚀 [BaseDeDatos] Configured API URL:", API_URL);
```

**Escenarios de Uso:**

| Escenario | Configuración | IP/Host | Cuándo Usar |
|-----------|---------------|---------|-------------|
| **Testing en dispositivo físico** | Manual IP | 192.168.X.X | Desarrollo con celular en misma red |
| **Emulador Android** | Android Bridge | 10.0.2.2 | Pruebas en emulador |
| **Simulador iOS** | Localhost | localhost | Pruebas en simulador Mac |
| **Web Browser** | Localhost | localhost | Desarrollo web |
| **Producción** | Dominio | https://api.goride.com | Deploy final |

### 🔄 Flujo de Uso del Controlador

```
┌────────────────────────────────────────────────────────────────┐
│              EJEMPLO: SOLICITAR UN VIAJE                       │
└────────────────────────────────────────────────────────────────┘

1. Vista (DashboardVista.tsx)
   │
   │ const solicitarViaje = async () => {
   │   const resultado = await BaseDeDatos.solicitarViaje(
   │     user.id, origen, destino, precio, "viaje"
   │   );
   │ }
   │
   ▼
2. Controlador (BaseDeDatos.ts)
   │
   │ solicitarViaje: async (usuario_id, origen, destino, precio, tipo) => {
   │   try {
   │     const response = await fetch(`${API_URL}/viajes/solicitar`, {
   │       method: "POST",
   │       headers: { "Content-Type": "application/json" },
   │       body: JSON.stringify({ usuario_id, origen, destino, precio, tipo })
   │     });
   │     return await response.json();
   │   } catch (error) {
   │     return { success: false, message: "Error de conexión" };
   │   }
   │ }
   │
   ▼
3. Transporte HTTP
   │
   │ POST http://172.25.3.48:3000/api/viajes/solicitar
   │ Content-Type: application/json
   │ Body: { usuario_id: 1, origen: "...", destino: "...", ... }
   │
   ▼
4. Backend (server.js)
   │
   │ app.post("/api/viajes/solicitar", (req, res) => {
   │   // Buscar conductor cercano
   │   // Crear registro en BD
   │   // Retornar resultado
   │   res.json({ success: true, viaje_id: 123, conductor_id: 5 });
   │ });
   │
   ▼
5. Respuesta al Controlador
   │
   │ { success: true, viaje_id: 123, conductor_id: 5 }
   │
   ▼
6. Vista actualiza UI
   │
   │ if (resultado.success) {
   │   setFase("en_viaje");
   │   mostrarConductor(resultado.conductor_id);
   │ }
   │
   ▼
Usuario ve conductor asignado en el mapa
```

### 📋 Ejemplo de Implementación Completa

```typescript
// 📍 Vista: DashboardVista.tsx
import { BaseDeDatos } from "../controladores/BaseDeDatos";

const DashboardVista = () => {
  const { user } = useAuth();
  const [viaje, setViaje] = useState(null);
  
  const solicitarViaje = async () => {
    // 1. Mostrar loading
    setLoading(true);
    
    // 2. Invocar controlador
    const resultado = await BaseDeDatos.solicitarViaje(
      user.id,
      "Av. Cristo Redentor",
      "Mall Ventura",
      15.50,
      "viaje",
      null,
      -17.7833,
      -63.1821
    );
    
    // 3. Manejar resultado
    if (resultado.success) {
      setViaje(resultado);
      Alert.alert("Éxito", "Conductor asignado");
      navegarAViaje();
    } else {
      Alert.alert("Error", resultado.message);
    }
    
    setLoading(false);
  };
  
  return (
    <TouchableOpacity onPress={solicitarViaje}>
      <Text>Solicitar Viaje</Text>
    </TouchableOpacity>
  );
};
```

### 🎯 Ventajas del Patrón Controlador

| Ventaja | Descripción | Impacto |
|---------|-------------|---------|
| **Centralización** | Un solo punto de acceso al backend | Mantenimiento simplificado |
| **Reutilización** | Métodos usados por múltiples vistas | Menos duplicación de código |
| **Abstracción** | Vistas no conocen detalles de HTTP | Desacoplamiento |
| **Testabilidad** | Fácil hacer mocks del controlador | Testing unitario |
| **Consistencia** | Todos los errores se manejan igual | UX uniforme |
| **Logging** | Punto único para logs de red | Debugging eficiente |
| **Configuración** | Cambiar URL sin tocar vistas | Flexibilidad de deployment |

### 🔒 Seguridad en el Controlador

```typescript
// ✅ 1. Validación de datos antes de enviar
solicitarViaje: async (...) => {
  // Validar que los datos no estén vacíos
  if (!usuario_id || !origen || !destino) {
    return { success: false, message: "Datos incompletos" };
  }
  
  // Continuar con la petición...
}

// ✅ 2. Sanitización de entrada
actualizarPerfil: async (usuario_id, data) => {
  // Limpiar caracteres especiales
  const sanitizedData = {
    nombre: data.nombre.trim(),
    email: data.email.toLowerCase().trim(),
    telefono: data.telefono.replace(/[^0-9]/g, '')
  };
  
  // Enviar datos sanitizados
}

// ✅ 3. Headers de seguridad (Producción)
const headers = {
  'Content-Type': 'application/json',
  'Authorization': `Bearer ${token}`,  // JWT Token
  'X-API-Key': process.env.API_KEY     // API Key
};
```

---

## 7.3 SISTEMA DE ROLES Y PERMISOS

GoRide implementa un sistema de control de acceso basado en roles (RBAC - Role-Based Access Control) que diferencia entre usuarios pasajeros y conductores. Este sistema determina qué funcionalidades están disponibles para cada tipo de usuario, controlando el acceso a rutas, endpoints y características específicas de la aplicación.

### 👥 Roles del Sistema

```
┌─────────────────────────────────────────────────────────────────┐
│                   JERARQUÍA DE ROLES                            │
└─────────────────────────────────────────────────────────────────┘

                    ┌─────────────┐
                    │   USUARIO   │
                    │  (Genérico) │
                    └──────┬──────┘
                           │
            ┌──────────────┴──────────────┐
            │                             │
            ▼                             ▼
    ┌───────────────┐            ┌───────────────┐
    │   PASAJERO    │            │   CONDUCTOR   │
    │ es_conductor: 0│            │es_conductor: 1│
    └───────────────┘            └───────────────┘
            │                             │
            │                             │
    ┌───────▼───────┐            ┌───────▼───────┐
    │  PERMISOS:    │            │  PERMISOS:    │
    │ • Solicitar   │            │ • Todo lo de  │
    │   viajes      │            │   Pasajero    │
    │ • Ver historial│            │ • Aceptar     │
    │ • Calificar   │            │   viajes      │
    │ • Chat        │            │ • Ver         │
    │ • Métodos pago│            │   pendientes  │
    │ • Lugares     │            │ • Actualizar  │
    │ • Soporte     │            │   estado      │
    │               │            │ • Panel       │
    │               │            │   conductor   │
    └───────────────┘            └───────────────┘
```

### 📊 Estructura de Datos de Roles

#### Modelo de Usuario

```typescript
// 📍 src/modelos/Usuario.ts

export interface Usuario {
  id: number;
  nombre: string;
  email: string;
  telefono?: string;
  foto_perfil?: string;
  es_conductor: boolean;      // 0 = Pasajero, 1 = Conductor
  conductor_id?: number;      // ID en tabla conductores (si es conductor)
  created_at?: string;
}
```

#### Tabla de Base de Datos

```sql
-- Tabla usuarios: Define el rol base
CREATE TABLE usuarios (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  telefono VARCHAR(20),
  password VARCHAR(255) NOT NULL,
  foto_perfil LONGTEXT,
  es_conductor BOOLEAN DEFAULT FALSE,  -- ROL PRINCIPAL
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tabla conductores: Detalles adicionales solo para conductores
CREATE TABLE conductores (
  id INT AUTO_INCREMENT PRIMARY KEY,
  usuario_id INT NOT NULL,              -- FK a usuarios
  modelo_auto VARCHAR(100),
  placa_auto VARCHAR(20),
  licencia VARCHAR(50),
  calificacion DECIMAL(3, 2) DEFAULT 5.00,
  estado ENUM('activo', 'inactivo', 'en_viaje') DEFAULT 'inactivo',
  latitud DECIMAL(10, 8),
  longitud DECIMAL(11, 8),
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE CASCADE
);
```

### 🔐 Control de Acceso por Rol

#### Tabla de Permisos

| Funcionalidad | Pasajero | Conductor | Endpoint | Validación |
|---------------|----------|-----------|----------|------------|
| **Solicitar viaje** | ✅ | ✅ | POST `/api/viajes/solicitar` | `usuario_id` válido |
| **Ver historial** | ✅ | ✅ | GET `/api/viajes/historial/:id` | `usuario_id` coincide |
| **Aceptar viaje** | ❌ | ✅ | POST `/api/viajes/aceptar` | `es_conductor == 1` |
| **Ver pendientes** | ❌ | ✅ | GET `/api/viajes/pendientes` | `es_conductor == 1` |
| **Cambiar estado** | ❌ | ✅ | POST `/api/conductores/estado` | `conductor_id` válido |
| **Panel conductor** | ❌ | ✅ | Ruta `/conductor` | `user.conductor_id` existe |
| **Calificar viaje** | ✅ | ✅ | POST `/api/calificaciones` | Participó en el viaje |
| **Chat** | ✅ | ✅ | GET/POST `/api/mensajes` | Vinculado al viaje |
| **Agregar pago** | ✅ | ✅ | POST `/api/metodos_pago` | Propio usuario |
| **Billetera** | ✅ | ✅ (extra) | Ruta `/billetera` | Conductor ve ganancias |

### 🚦 Implementación de Control de Acceso

#### 1. Validación en Frontend (React Native)

```typescript
// 📍 src/vistas/DashboardVista.tsx

export default function DashboardVista() {
  const { user } = useAuth();
  
  // Mostrar panel según rol
  if (user?.es_conductor) {
    return (
      <View>
        {/* Dashboard de Pasajero */}
        <Button title="Solicitar Viaje" onPress={solicitarViaje} />
        
        {/* Opción adicional para conductor */}
        <Button 
          title="Modo Conductor" 
          onPress={() => router.push('/conductor')}
          style={styles.conductorButton}
        />
      </View>
    );
  }
  
  return (
    <View>
      {/* Solo dashboard de pasajero */}
      <Button title="Solicitar Viaje" onPress={solicitarViaje} />
    </View>
  );
}
```

#### 2. Validación en Vistas Específicas

```typescript
// 📍 src/vistas/ConductorVista.tsx

export default function ConductorVista() {
  const { user } = useAuth();
  
  // Verificar que tiene perfil de conductor
  if (!user?.conductor_id) {
    return (
      <View>
        <Text>No tienes acceso a esta sección</Text>
        <Button title="Volver" onPress={() => router.back()} />
      </View>
    );
  }
  
  // Verificar que el rol es correcto
  if (!user?.es_conductor) {
    Alert.alert("Error", "No tienes perfil de conductor activo.");
    router.back();
    return null;
  }
  
  // Resto del código del panel de conductor
  return (
    <View>
      <Switch value={conectado} onValueChange={toggleConexion} />
      {/* ... */}
    </View>
  );
}
```

#### 3. Validación en Controlador

```typescript
// 📍 src/controladores/BaseDeDatos.ts

actualizarEstadoConductor: async (
  conductor_id: number,
  estado: "activo" | "inactivo" | "en_viaje"
) => {
  // Validar que conductor_id existe
  if (!conductor_id) {
    return { success: false, message: "ID de conductor inválido" };
  }
  
  try {
    const response = await fetch(`${API_URL}/conductores/estado`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ conductor_id, estado }),
    });
    return await response.json();
  } catch (error) {
    return { success: false, message: "Error de conexión" };
  }
}
```

#### 4. Validación en Backend (Server-side)

```javascript
// 📍 backend/server.js

// Middleware de autenticación (Recomendado)
const verificarConductor = (req, res, next) => {
  const { conductor_id } = req.body;
  
  // Verificar que conductor existe y es válido
  db.execute(
    "SELECT c.*, u.es_conductor FROM conductores c JOIN usuarios u ON c.usuario_id = u.id WHERE c.id = ?",
    [conductor_id],
    (err, results) => {
      if (err || results.length === 0) {
        return res.status(403).json({ 
          success: false, 
          message: "No autorizado" 
        });
      }
      
      if (!results[0].es_conductor) {
        return res.status(403).json({ 
          success: false, 
          message: "Usuario no es conductor" 
        });
      }
      
      req.conductor = results[0];
      next();
    }
  );
};

// Aplicar middleware a rutas de conductor
app.post("/api/conductores/estado", verificarConductor, (req, res) => {
  const { estado } = req.body;
  const conductor = req.conductor;
  
  db.execute(
    "UPDATE conductores SET estado = ? WHERE id = ?",
    [estado, conductor.id],
    (err) => {
      if (err) return res.status(500).json({ error: err.message });
      res.json({ success: true, message: "Estado actualizado" });
    }
  );
});

// Validar viajes pendientes solo para conductores
app.get("/api/viajes/pendientes", (req, res) => {
  // En producción, validar con JWT que es conductor
  const query = `
    SELECT v.*, u.nombre as pasajero_nombre 
    FROM viajes v 
    JOIN usuarios u ON v.usuario_id = u.id 
    WHERE v.estado = "solicitado"
  `;
  
  db.execute(query, (err, results) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json({ success: true, viajes: results });
  });
});
```

### 🎯 Casos de Uso por Rol

#### Caso 1: Usuario Pasajero Solicita Viaje

```typescript
// ✅ PERMITIDO
const { user } = useAuth();  // user.es_conductor = 0

const resultado = await BaseDeDatos.solicitarViaje(
  user.id,
  origen,
  destino,
  precio
);

// Backend procesa normalmente
// ✅ Viaje creado con éxito
```

#### Caso 2: Pasajero Intenta Acceder al Panel de Conductor

```typescript
// ❌ BLOQUEADO EN FRONTEND
const { user } = useAuth();  // user.es_conductor = 0

if (!user.conductor_id) {
  // No se muestra el botón "Modo Conductor"
  return null;
}

// Si intenta navegar directamente
router.push('/conductor');

// ConductorVista.tsx detecta y bloquea
if (!user?.conductor_id) {
  Alert.alert("Acceso denegado");
  router.back();
}
```

#### Caso 3: Conductor Acepta Viaje

```typescript
// ✅ PERMITIDO
const { user } = useAuth();  // user.es_conductor = 1, conductor_id = 5

const resultado = await BaseDeDatos.aceptarViaje(viaje_id, user.conductor_id);

// Backend valida:
// 1. conductor_id existe
// 2. Viaje está disponible
// 3. Actualiza BD
// ✅ Viaje aceptado
```

#### Caso 4: Conductor Cambia su Estado

```typescript
// ✅ PERMITIDO
const { user } = useAuth();  // user.conductor_id = 5

const resultado = await BaseDeDatos.actualizarEstadoConductor(
  user.conductor_id,
  "activo"
);

// Backend actualiza tabla conductores
// UPDATE conductores SET estado = 'activo' WHERE id = 5
// ✅ Estado actualizado
```

### 📊 Matriz de Permisos Detallada

| Acción | Pasajero | Conductor | Validación Frontend | Validación Backend |
|--------|----------|-----------|--------------------|--------------------|
| Login | ✅ | ✅ | Email/Password | Credenciales en BD |
| Registro | ✅ | ✅ | Campos requeridos | Email único |
| Dashboard | ✅ | ✅ | Autenticado | N/A |
| Solicitar viaje | ✅ | ✅ | `user.id` existe | `usuario_id` válido |
| Ver historial | ✅ | ✅ | Autenticado | Propios viajes |
| Calificar viaje | ✅ | ✅ | Participó en viaje | Viaje completado |
| Panel conductor | ❌ | ✅ | `user.conductor_id` | `es_conductor == 1` |
| Ver pendientes | ❌ | ✅ | `user.conductor_id` | Estado activo |
| Aceptar viaje | ❌ | ✅ | `user.conductor_id` | Viaje disponible |
| Cambiar estado | ❌ | ✅ | `user.conductor_id` | Conductor válido |
| Actualizar ubicación | ❌ | ✅ | `user.conductor_id` | GPS activo |
| Ver ganancias | ❌ | ✅ | `user.es_conductor` | Viajes propios |

### 🔄 Cambio de Rol Dinámico

```typescript
// Convertir pasajero en conductor
const convertirAConductor = async (usuario_id: number) => {
  // 1. Actualizar campo es_conductor
  await db.execute(
    "UPDATE usuarios SET es_conductor = 1 WHERE id = ?",
    [usuario_id]
  );
  
  // 2. Crear registro en tabla conductores
  const result = await db.execute(
    "INSERT INTO conductores (usuario_id, estado) VALUES (?, 'inactivo')",
    [usuario_id]
  );
  
  const conductor_id = result.insertId;
  
  // 3. Actualizar AuthContext
  await updateUser({ 
    es_conductor: 1, 
    conductor_id: conductor_id 
  });
  
  return { success: true, conductor_id };
};
```

### 🛡️ Mejores Prácticas de Seguridad

```typescript
// ✅ 1. NUNCA confiar solo en validación frontend
// Backend SIEMPRE debe validar permisos

// ❌ MAL (Solo frontend)
if (user.es_conductor) {
  // Mostrar panel conductor
}

// ✅ BIEN (Frontend + Backend)
// Frontend
if (user.es_conductor) {
  // Mostrar panel
}

// Backend
app.get("/api/viajes/pendientes", verificarToken, verificarConductor, (req, res) => {
  // Solo llega aquí si es conductor válido
});

// ✅ 2. Usar JWT con rol incluido
const token = jwt.sign({
  userId: user.id,
  role: user.es_conductor ? 'conductor' : 'pasajero',
  conductorId: user.conductor_id
}, SECRET_KEY);

// ✅ 3. Middleware de autorización
const requireRole = (role) => {
  return (req, res, next) => {
    if (req.user.role !== role) {
      return res.status(403).json({ message: "No autorizado" });
    }
    next();
  };
};

app.post("/api/viajes/aceptar", requireRole('conductor'), (req, res) => {
  // Solo conductores llegan aquí
});
```

---

## 7.4 PROTECCIÓN DE LA INFORMACIÓN LOCAL

La protección de datos en el dispositivo móvil es crucial para salvaguardar la información sensible del usuario. GoRide implementa múltiples capas de seguridad local utilizando AsyncStorage, validación de datos y prácticas recomendadas de almacenamiento seguro, aunque con oportunidades de mejora hacia soluciones más robustas.

### 🗄️ Almacenamiento Local con AsyncStorage

```
┌─────────────────────────────────────────────────────────────────┐
│              ARQUITECTURA DE ALMACENAMIENTO LOCAL               │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│               React Native App (Memoria RAM)                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  AuthContext (Estado Global)                        │   │
│  │  • user: User | null                                │   │
│  │  • isLoading: boolean                               │   │
│  └────────────────────┬────────────────────────────────┘   │
│                       │ setUser() / getUser()              │
│                       │                                    │
└───────────────────────┼────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│          AsyncStorage (@react-native-async-storage)         │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Almacenamiento Clave-Valor                         │   │
│  │  ┌──────────────────────────────────────────────┐   │   │
│  │  │ Key: "user_session"                          │   │   │
│  │  │ Value: JSON.stringify({                     │   │   │
│  │  │   id: 1,                                     │   │   │
│  │  │   nombre: "Juan",                            │   │   │
│  │  │   email: "juan@mail.com",                    │   │   │
│  │  │   es_conductor: 0,                           │   │   │
│  │  │   foto_perfil: "base64...",                  │   │   │
│  │  │   conductor_id: null                         │   │   │
│  │  │ })                                           │   │   │
│  │  └──────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────┘   │
└───────────────────────┬─────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────────────────┐
│           Sistema de Archivos del Dispositivo               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Android: /data/data/[package]/files/RCTAsyncLocalStorage│
│  │ iOS: ~/Library/Preferences/[bundle-id]              │   │
│  │                                                      │   │
│  │ Archivo: manifest.json + archivos de datos         │   │
│  │ Permisos: Solo la app puede acceder (sandbox)      │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 📊 Datos Almacenados Localmente

#### Tabla de Información Persistida

| Dato | Tipo | Sensibilidad | Almacenamiento | Cifrado | Tiempo de Vida |
|------|------|--------------|----------------|---------|----------------|
| **ID de usuario** | number | 🟡 Media | AsyncStorage | ❌ No | Hasta logout |
| **Nombre completo** | string | 🟡 Media | AsyncStorage | ❌ No | Hasta logout |
| **Email** | string | 🟠 Alta | AsyncStorage | ❌ No | Hasta logout |
| **Teléfono** | string | 🟠 Alta | AsyncStorage | ❌ No | Hasta logout |
| **Foto de perfil** | Base64 | 🟢 Baja | AsyncStorage | ❌ No | Hasta logout |
| **Rol (es_conductor)** | boolean | 🟡 Media | AsyncStorage | ❌ No | Hasta logout |
| **Conductor ID** | number \| null | 🟡 Media | AsyncStorage | ❌ No | Hasta logout |
| **Contraseña** | string | 🔴 Crítica | ❌ NUNCA | N/A | N/A |
| **Token sesión** | string | 🔴 Crítica | ⚠️ TODO | ✅ Sí | 7 días |

### 🔐 Implementación Actual de Seguridad

#### Código de Almacenamiento

```typescript
// 📍 src/context/AuthContext.tsx

// GUARDAR sesión
const login = async (email: string, password: string) => {
  const result = await BaseDeDatos.login(email, password);
  
  if (result.success && result.user) {
    setUser(result.user);
    
    // Persistir en AsyncStorage
    await AsyncStorage.setItem(
      "user_session",              // Key
      JSON.stringify(result.user)  // Value (JSON)
    );
    
    return { success: true };
  }
};

// RECUPERAR sesión
const checkUser = async () => {
  try {
    const storedUser = await AsyncStorage.getItem("user_session");
    
    if (storedUser) {
      const userData = JSON.parse(storedUser);
      setUser(userData);
    }
  } catch (error) {
    console.error("Error al recuperar sesión:", error);
  } finally {
    setIsLoading(false);
  }
};

// ELIMINAR sesión
const logout = async () => {
  setUser(null);
  await AsyncStorage.removeItem("user_session");
};

// ACTUALIZAR sesión
const updateUser = async (userData: Partial<User>) => {
  if (user) {
    const updatedUser = { ...user, ...userData };
    setUser(updatedUser);
    await AsyncStorage.setItem(
      "user_session",
      JSON.stringify(updatedUser)
    );
  }
};
```

### 🛡️ Nivel de Seguridad Actual

#### Análisis de Vulnerabilidades

| Aspecto | Estado Actual | Nivel de Riesgo | Impacto |
|---------|---------------|-----------------|---------|
| **Cifrado de datos** | Sin cifrado | 🔴 Alto | Datos legibles en texto plano |
| **Contraseñas almacenadas** | ✅ No se almacenan | 🟢 Ninguno | Sin riesgo |
| **Email y teléfono** | Sin cifrar | 🟠 Medio | Exposición de PII |
| **Tokens de sesión** | ❌ No implementado | 🟡 Medio | Sin expiración automática |
| **Validación de integridad** | ❌ No | 🟠 Medio | Posible manipulación |
| **Backup de datos** | Por defecto del OS | 🟡 Medio | Puede incluirse en backups |
| **Root/Jailbreak detection** | ❌ No | 🟠 Medio | Vulnerable en dispositivos comprometidos |

### 🔒 Mejoras de Seguridad Recomendadas

#### 1. Expo SecureStore (Cifrado Nativo)

```typescript
// ✅ RECOMENDADO: Usar SecureStore para datos sensibles
import * as SecureStore from 'expo-secure-store';

// Guardar con cifrado nativo
const saveSecureSession = async (user: User) => {
  try {
    // iOS: Keychain
    // Android: EncryptedSharedPreferences
    await SecureStore.setItemAsync(
      'secure_user_session',
      JSON.stringify(user)
    );
  } catch (error) {
    console.error('Error al guardar sesión segura:', error);
  }
};

// Recuperar
const getSecureSession = async () => {
  try {
    const session = await SecureStore.getItemAsync('secure_user_session');
    return session ? JSON.parse(session) : null;
  } catch (error) {
    console.error('Error al recuperar sesión:', error);
    return null;
  }
};

// Eliminar
const deleteSecureSession = async () => {
  await SecureStore.deleteItemAsync('secure_user_session');
};
```

**Ventajas de SecureStore:**
- ✅ Cifrado nativo del OS (Keychain en iOS, Keystore en Android)
- ✅ Protegido contra acceso no autorizado
- ✅ No incluido en backups del dispositivo
- ✅ Requiere autenticación biométrica (opcional)

#### 2. Tokens JWT con Expiración

```typescript
// Backend: Generar token con expiración
const jwt = require('jsonwebtoken');

app.post("/api/login", (req, res) => {
  // Validar usuario...
  
  if (userValid) {
    const token = jwt.sign(
      { 
        userId: user.id,
        role: user.es_conductor ? 'conductor' : 'pasajero'
      },
      process.env.JWT_SECRET,
      { expiresIn: '7d' }  // Expira en 7 días
    );
    
    res.json({
      success: true,
      user: { ...user, password: undefined },
      token: token,
      expiresAt: Date.now() + (7 * 24 * 60 * 60 * 1000)
    });
  }
});

// Frontend: Almacenar y validar token
const login = async (email: string, password: string) => {
  const result = await BaseDeDatos.login(email, password);
  
  if (result.success) {
    // Guardar token cifrado
    await SecureStore.setItemAsync('auth_token', result.token);
    await SecureStore.setItemAsync('token_expires', result.expiresAt.toString());
    
    setUser(result.user);
  }
};

// Validar expiración al iniciar app
const checkTokenExpiration = async () => {
  const expiresAt = await SecureStore.getItemAsync('token_expires');
  
  if (expiresAt && Date.now() > parseInt(expiresAt)) {
    // Token expirado, forzar logout
    await logout();
    Alert.alert('Sesión expirada', 'Por favor inicia sesión nuevamente');
  }
};
```

#### 3. Validación de Integridad

```typescript
// Generar hash de los datos para detectar manipulación
import CryptoJS from 'crypto-js';

const saveWithIntegrity = async (key: string, data: any) => {
  const dataString = JSON.stringify(data);
  
  // Generar hash SHA256
  const hash = CryptoJS.SHA256(dataString).toString();
  
  // Guardar datos + hash
  await AsyncStorage.setItem(key, dataString);
  await AsyncStorage.setItem(`${key}_hash`, hash);
};

const getWithIntegrity = async (key: string) => {
  const dataString = await AsyncStorage.getItem(key);
  const storedHash = await AsyncStorage.getItem(`${key}_hash`);
  
  if (!dataString || !storedHash) return null;
  
  // Verificar integridad
  const computedHash = CryptoJS.SHA256(dataString).toString();
  
  if (computedHash !== storedHash) {
    console.error('⚠️ Datos manipulados detectados');
    await AsyncStorage.removeItem(key);
    await AsyncStorage.removeItem(`${key}_hash`);
    return null;
  }
  
  return JSON.parse(dataString);
};
```

#### 4. Detección de Dispositivos Comprometidos

```typescript
// Instalar: expo-device
import * as Device from 'expo-device';

const checkDeviceSecurity = async () => {
  // Verificar si el dispositivo está rooteado/jailbroken
  // (Requiere librerías nativas adicionales como react-native-jail-monkey)
  
  const isEmulator = !Device.isDevice;
  
  if (isEmulator) {
    console.warn('⚠️ Ejecutándose en emulador');
  }
  
  // En producción, podríamos bloquear funcionalidades sensibles
  if (isDeviceCompromised()) {
    Alert.alert(
      'Advertencia de Seguridad',
      'Tu dispositivo puede estar comprometido. Algunas funciones estarán limitadas.'
    );
  }
};
```

### 📊 Comparación de Métodos de Almacenamiento

| Método | Cifrado | Plataforma | Velocidad | Capacidad | Uso Recomendado |
|--------|---------|-----------|-----------|-----------|-----------------|
| **AsyncStorage** | ❌ No | iOS, Android, Web | ⚡ Rápido | ~6MB | Datos no sensibles |
| **SecureStore** | ✅ Nativo | iOS, Android | ⚡ Rápido | 2KB por ítem | Tokens, credenciales |
| **FileSystem + Cifrado** | ✅ Manual | Todas | 🐌 Lento | Ilimitada | Archivos grandes |
| **SQLite cifrado** | ✅ Sí | Todas | ⚡ Medio | Ilimitada | Datos estructurados |
| **Memoria RAM** | ⚠️ Volátil | Todas | ⚡⚡ Muy rápido | Limitada | Datos temporales |

### 🔍 Auditoría de Datos Almacenados

```typescript
// Herramienta de debugging para ver qué hay en AsyncStorage
const auditLocalStorage = async () => {
  try {
    const keys = await AsyncStorage.getAllKeys();
    console.log('📦 Keys almacenadas:', keys);
    
    for (const key of keys) {
      const value = await AsyncStorage.getItem(key);
      console.log(`🔑 ${key}:`, value?.substring(0, 100));
    }
  } catch (error) {
    console.error('Error en auditoría:', error);
  }
};

// Limpiar todo el almacenamiento (útil para testing)
const clearAllStorage = async () => {
  await AsyncStorage.clear();
  console.log('🗑️ AsyncStorage limpiado');
};
```

### 🛡️ Mejores Prácticas Implementadas

| Práctica | Estado | Descripción |
|----------|--------|-------------|
| ✅ **No guardar contraseñas** | Implementado | Las contraseñas NUNCA se almacenan localmente |
| ✅ **Logout limpia datos** | Implementado | `removeItem()` al cerrar sesión |
| ✅ **Try-catch en operaciones** | Implementado | Errores capturados y logueados |
| ⚠️ **Datos sensibles cifrados** | Pendiente | Migrar a SecureStore |
| ⚠️ **Tokens con expiración** | Pendiente | Implementar JWT |
| ⚠️ **Validación de integridad** | Pendiente | Hash de datos críticos |
| ❌ **Detección de root** | No implementado | Agregar react-native-jail-monkey |
| ❌ **Biometría** | No implementado | Autenticación adicional con huella/Face ID |

### 🎯 Recomendaciones de Seguridad por Prioridad

#### Prioridad ALTA (Implementar inmediatamente)

1. **Migrar a SecureStore para datos sensibles**
   ```bash
   expo install expo-secure-store
   ```

2. **Implementar JWT con expiración**
   - Backend: Generar tokens firmados
   - Frontend: Validar expiración al iniciar app

3. **Cifrar contraseñas en BD (Backend)**
   ```bash
   npm install bcrypt
   ```

#### Prioridad MEDIA (Próximas versiones)

4. **Validación de integridad de datos**
5. **Rate limiting en backend**
6. **HTTPS en producción**
7. **Logging de actividades sospechosas**

#### Prioridad BAJA (Mejoras futuras)

8. **Autenticación biométrica**
9. **Detección de dispositivos rooteados**
10. **Encriptación de mensajes de chat**

### 📱 Ejemplo Completo de Implementación Segura

```typescript
// 📍 src/context/SecureAuthContext.tsx

import * as SecureStore from 'expo-secure-store';
import CryptoJS from 'crypto-js';

const KEYS = {
  USER: 'secure_user',
  TOKEN: 'auth_token',
  TOKEN_EXPIRES: 'token_expires',
};

export const SecureAuthProvider = ({ children }) => {
  const [user, setUser] = useState<User | null>(null);
  
  // Guardar sesión segura
  const secureLogin = async (email: string, password: string) => {
    const result = await BaseDeDatos.login(email, password);
    
    if (result.success) {
      // Guardar token cifrado
      await SecureStore.setItemAsync(KEYS.TOKEN, result.token);
      await SecureStore.setItemAsync(
        KEYS.TOKEN_EXPIRES,
        result.expiresAt.toString()
      );
      
      // Guardar usuario (sin datos sensibles)
      const { password: _, ...safeUser } = result.user;
      await SecureStore.setItemAsync(KEYS.USER, JSON.stringify(safeUser));
      
      setUser(safeUser);
      return { success: true };
    }
    
    return { success: false, message: result.message };
  };
  
  // Recuperar sesión segura
  const checkSecureSession = async () => {
    try {
      // Validar expiración
      const expiresAt = await SecureStore.getItemAsync(KEYS.TOKEN_EXPIRES);
      if (expiresAt && Date.now() > parseInt(expiresAt)) {
        await secureLogout();
        return;
      }
      
      // Recuperar usuario
      const userString = await SecureStore.getItemAsync(KEYS.USER);
      if (userString) {
        setUser(JSON.parse(userString));
      }
    } catch (error) {
      console.error('Error recuperando sesión:', error);
    }
  };
  
  // Logout seguro
  const secureLogout = async () => {
    await SecureStore.deleteItemAsync(KEYS.USER);
    await SecureStore.deleteItemAsync(KEYS.TOKEN);
    await SecureStore.deleteItemAsync(KEYS.TOKEN_EXPIRES);
    setUser(null);
  };
  
  useEffect(() => {
    checkSecureSession();
  }, []);
  
  return (
    <AuthContext.Provider value={{ user, secureLogin, secureLogout }}>
      {children}
    </AuthContext.Provider>
  );
};
```

---

## 🎯 Conclusiones del Capítulo VII

La arquitectura y seguridad de GoRide se fundamenta en:

1. **Arquitectura de tres capas** bien definida (Presentación, Negocio, Datos)
2. **Controlador centralizado** (`BaseDeDatos.ts`) como gateway único
3. **Sistema de roles** basado en `es_conductor` con validación en múltiples capas
4. **Almacenamiento local** con AsyncStorage, con oportunidades de mejora hacia SecureStore

### Próximos Pasos de Seguridad

- [ ] Migrar a **expo-secure-store**
- [ ] Implementar **JWT** con expiración
- [ ] Añadir **bcrypt** para contraseñas
- [ ] **HTTPS** en producción
- [ ] **Rate limiting** en endpoints
- [ ] **Logging** de seguridad
- [ ] **Autenticación biométrica**

---

**Documentación actualizada:** Enero 2026  
**Versión:** 1.0.0
