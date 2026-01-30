# 📱 GoRide - Documentación Técnica Avanzada

## 6.4 DESARROLLO DEL SISTEMA DE AUTENTICACIÓN

El sistema de autenticación implementa un flujo completo de registro, inicio de sesión y gestión de credenciales, utilizando Context API para el manejo de estado global y AsyncStorage para persistencia local. Esta arquitectura garantiza que la sesión del usuario se mantenga activa incluso después de cerrar la aplicación, proporcionando una experiencia de usuario fluida y segura.

### 🔐 Componentes del Sistema de Autenticación

| Componente | Ubicación | Responsabilidad | Tecnología |
|------------|-----------|-----------------|------------|
| **AuthContext** | [src/context/AuthContext.tsx](src/context/AuthContext.tsx) | Gestión de estado global de autenticación | Context API + React Hooks |
| **API Login** | [backend/server.js](backend/server.js) (POST /api/login) | Validación de credenciales en servidor | Express + MySQL |
| **API Register** | [backend/server.js](backend/server.js) (POST /api/register) | Creación de nuevos usuarios | Express + MySQL |
| **BaseDeDatos** | [src/controladores/BaseDeDatos.ts](src/controladores/BaseDeDatos.ts) | Controlador de peticiones HTTP | Axios + Fetch API |
| **LoginScreen** | [app/index.tsx](app/index.tsx) | Interfaz de inicio de sesión | React Native |
| **AsyncStorage** | AsyncStorage (React Native) | Persistencia local de sesión | @react-native-async-storage |

### 📊 Flujo de Autenticación

```
┌─────────────────────────────────────────────────────────────────┐
│                    1. INICIO DE SESIÓN (LOGIN)                  │
└─────────────────────────────────────────────────────────────────┘

Usuario ingresa credenciales
         │
         ▼
┌─────────────────────────────────────┐
│  LoginScreen (app/index.tsx)        │
│  - email: string                    │
│  - password: string                 │
└────────────┬────────────────────────┘
             │
             │ handleLogin()
             ▼
┌─────────────────────────────────────┐
│  AuthContext.login()                │
│  - Validación local                 │
│  - Invoca controlador               │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│  BaseDeDatos.login()                │
│  POST /api/login                    │
│  Body: { email, password }          │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│  Backend - Express Server           │
│  1. Buscar usuario en MySQL         │
│  2. Validar contraseña (plaintext)  │
│  3. Retornar datos del usuario      │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│  AuthContext recibe respuesta       │
│  - setUser(userData)                │
│  - AsyncStorage.setItem()           │
│  - Navegar a Dashboard              │
└─────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    2. REGISTRO (REGISTER)                       │
└─────────────────────────────────────────────────────────────────┘

Usuario completa formulario
         │
         ▼
┌─────────────────────────────────────┐
│  RegistroScreen (app/registro.tsx)  │
│  - nombre, email, password, tel     │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│  AuthContext.register()             │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│  BaseDeDatos.register()             │
│  POST /api/register                 │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│  Backend - Express Server           │
│  1. Verificar email no existe       │
│  2. INSERT INTO usuarios            │
│  3. Retornar usuario creado         │
└────────────┬────────────────────────┘
             │
             ▼
┌─────────────────────────────────────┐
│  Auto-login después de registro     │
│  - Sesión persistida                │
│  - Redirigir a Dashboard            │
└─────────────────────────────────────┘
```

### 🔑 Estructura del AuthContext

#### Tipos y Interfaces

```typescript
type User = {
  id: number;
  nombre: string;
  email: string;
  es_conductor: number;       // 0 = Pasajero, 1 = Conductor
  foto_perfil?: string;
  conductor_id?: number;
  telefono?: string;
  ciudad?: string;
};

type AuthContextType = {
  user: User | null;           // Usuario actual o null
  isLoading: boolean;          // Estado de carga inicial
  login: (email, password) => Promise<Result>;
  register: (data) => Promise<Result>;
  logout: () => Promise<void>;
  updateUser: (userData) => Promise<void>;
};
```

#### Métodos del AuthContext

| Método | Parámetros | Retorno | Función Principal |
|--------|-----------|---------|-------------------|
| `login()` | `email: string`<br>`password: string` | `Promise<{success: boolean, message?: string}>` | • Invoca API de login<br>• Almacena usuario en estado<br>• Persiste en AsyncStorage<br>• Retorna resultado |
| `register()` | `nombre: string`<br>`email: string`<br>`password: string`<br>`telefono: string` | `Promise<{success: boolean, message?: string}>` | • Invoca API de registro<br>• Auto-login post registro<br>• Persiste sesión |
| `logout()` | Ninguno | `Promise<void>` | • Limpia estado (setUser(null))<br>• Elimina AsyncStorage<br>• Redirige a login |
| `updateUser()` | `userData: Partial<User>` | `Promise<void>` | • Actualiza estado local<br>• Sincroniza con AsyncStorage |
| `checkUser()` | Ninguno (interno) | `Promise<void>` | • Recupera sesión al iniciar app<br>• Lee de AsyncStorage |

### 📡 Endpoints de Autenticación (Backend)

#### 1. POST /api/login

**Request:**
```json
{
  "email": "usuario@example.com",
  "password": "contraseña123"
}
```

**Response (Éxito):**
```json
{
  "success": true,
  "user": {
    "id": 1,
    "nombre": "Juan Pérez",
    "email": "usuario@example.com",
    "telefono": "12345678",
    "es_conductor": 0,
    "foto_perfil": null,
    "conductor_id": null
  }
}
```

**Response (Error):**
```json
{
  "success": false,
  "message": "Credenciales inválidas"
}
```

**Lógica del Servidor:**
```javascript
app.post("/api/login", (req, res) => {
  const { email, password } = req.body;
  
  db.execute(
    "SELECT * FROM usuarios WHERE email = ? AND password = ?",
    [email, password],
    (err, users) => {
      if (err) return res.status(500).json({ error: err.message });
      
      if (users.length > 0) {
        const user = users[0];
        // Obtener conductor_id si es conductor
        if (user.es_conductor) {
          db.execute(
            "SELECT id FROM conductores WHERE usuario_id = ?",
            [user.id],
            (err, conductores) => {
              user.conductor_id = conductores[0]?.id || null;
              res.json({ success: true, user });
            }
          );
        } else {
          res.json({ success: true, user });
        }
      } else {
        res.json({ success: false, message: "Credenciales inválidas" });
      }
    }
  );
});
```

#### 2. POST /api/register

**Request:**
```json
{
  "nombre": "María García",
  "email": "maria@example.com",
  "password": "password123",
  "telefono": "987654321"
}
```

**Response (Éxito):**
```json
{
  "success": true,
  "user": {
    "id": 5,
    "nombre": "María García",
    "email": "maria@example.com",
    "telefono": "987654321",
    "es_conductor": 0
  }
}
```

**Validaciones Implementadas:**

| Validación | Tipo | Mensaje de Error |
|------------|------|------------------|
| Email único | Base de datos | "El correo ya está registrado" |
| Campos requeridos | Frontend | "Por favor completa todos los campos" |
| Formato email | Frontend | "Ingresa un correo válido" |
| Conexión | Red | "Error de conexión" |

### 🗄️ Persistencia de Sesión con AsyncStorage

#### Almacenamiento Local

```typescript
// Guardar sesión
await AsyncStorage.setItem("user_session", JSON.stringify(userData));

// Recuperar sesión al iniciar
const storedUser = await AsyncStorage.getItem("user_session");
if (storedUser) {
  setUser(JSON.parse(storedUser));
}

// Eliminar sesión (logout)
await AsyncStorage.removeItem("user_session");
```

#### Ciclo de Vida de la Sesión

| Evento | Acción | Resultado |
|--------|--------|-----------|
| **App inicia** | `checkUser()` ejecuta en useEffect | Sesión restaurada automáticamente |
| **Login exitoso** | `setItem("user_session", user)` | Sesión guardada |
| **Cierre de app** | AsyncStorage persiste | Datos conservados |
| **Reapertura** | Lee AsyncStorage | Usuario mantiene sesión |
| **Logout** | `removeItem("user_session")` | Sesión eliminada |

### 🛡️ Seguridad y Mejoras Recomendadas

#### Estado Actual

| Aspecto | Implementación Actual | Nivel de Seguridad |
|---------|----------------------|-------------------|
| **Almacenamiento de contraseñas** | Texto plano en BD | ⚠️ Bajo |
| **Transmisión** | HTTP sin cifrado | ⚠️ Bajo |
| **Tokens de sesión** | No implementado | ⚠️ Básico |
| **Validación de entrada** | Frontend básica | ⚠️ Medio |

#### Mejoras Recomendadas

```typescript
// ✅ 1. Hash de contraseñas (Backend)
const bcrypt = require('bcrypt');

// Registro
const hashedPassword = await bcrypt.hash(password, 10);
db.execute("INSERT INTO usuarios (..., password) VALUES (..., ?)", [hashedPassword]);

// Login
const match = await bcrypt.compare(password, user.password);

// ✅ 2. JWT Tokens
const jwt = require('jsonwebtoken');

const token = jwt.sign({ userId: user.id }, SECRET_KEY, { expiresIn: '7d' });
res.json({ success: true, token, user });

// ✅ 3. Middleware de Autenticación
const verifyToken = (req, res, next) => {
  const token = req.headers['authorization'];
  if (!token) return res.status(403).send('Token requerido');
  
  jwt.verify(token, SECRET_KEY, (err, decoded) => {
    if (err) return res.status(401).send('Token inválido');
    req.userId = decoded.userId;
    next();
  });
};

app.get('/api/perfil', verifyToken, (req, res) => {
  // req.userId disponible
});
```

### 📱 Implementación en la Vista (LoginScreen)

```typescript
export default function LoginScreen() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [loading, setLoading] = useState(false);
  const { login } = useAuth();

  const handleLogin = async () => {
    // 1. Validación local
    if (!email || !password) {
      Alert.alert("Error", "Por favor ingresa correo y contraseña");
      return;
    }

    // 2. Mostrar indicador de carga
    setLoading(true);

    try {
      // 3. Invocar AuthContext
      const result = await login(email, password);

      // 4. Manejar respuesta
      if (result.success) {
        router.replace("/(tabs)");  // Navegar a Dashboard
      } else {
        Alert.alert("Error", result.message || "Credenciales incorrectas");
      }
    } catch (error) {
      Alert.alert("Error", "Ocurrió un error al conectar con el servidor");
    } finally {
      setLoading(false);
    }
  };

  return (
    <View>
      <TextInput
        placeholder="Correo electrónico"
        value={email}
        onChangeText={setEmail}
        autoCapitalize="none"
        keyboardType="email-address"
      />
      <TextInput
        placeholder="Contraseña"
        value={password}
        onChangeText={setPassword}
        secureTextEntry
      />
      <TouchableOpacity onPress={handleLogin} disabled={loading}>
        <Text>{loading ? "Cargando..." : "Iniciar Sesión"}</Text>
      </TouchableOpacity>
    </View>
  );
}
```

### 🔄 Estados de Autenticación

```typescript
┌─────────────────────────────────────────────────┐
│  isLoading: true                                │
│  user: null                                     │
│  Estado: Verificando sesión guardada           │
└────────────────┬────────────────────────────────┘
                 │
       ┌─────────┴─────────┐
       │                   │
       ▼                   ▼
┌──────────────┐    ┌──────────────┐
│ Sesión Existe│    │ No Hay Sesión│
└──────┬───────┘    └──────┬───────┘
       │                   │
       ▼                   ▼
┌──────────────┐    ┌──────────────┐
│ isLoading:   │    │ isLoading:   │
│   false      │    │   false      │
│ user: User   │    │ user: null   │
│ → Dashboard  │    │ → Login      │
└──────────────┘    └──────────────┘
```

---

## 6.5 DESARROLLO DE LOS MÓDULOS CRUD

La aplicación implementa operaciones CRUD (Create, Read, Update, Delete) completas para gestionar las entidades principales del sistema: usuarios, viajes, conductores, métodos de pago, lugares guardados y mensajes. Cada módulo sigue una estructura RESTful con endpoints específicos en el backend y controladores correspondientes en el frontend.

### 📊 Módulos CRUD Implementados

| Módulo | Entidad | Tabla MySQL | Operaciones | Endpoints |
|--------|---------|-------------|-------------|-----------|
| **Usuarios** | Usuario/Perfil | `usuarios` | C R U | `/api/usuarios` |
| **Viajes** | Solicitud de viaje | `viajes` | C R U | `/api/viajes` |
| **Conductores** | Perfil de conductor | `conductores` | C R U | `/api/conductores` |
| **Métodos de Pago** | Tarjetas/cuentas | `metodos_pago` | C R D | `/api/metodos_pago` |
| **Lugares** | Direcciones favoritas | `lugares_guardados` | C R D | `/api/lugares` |
| **Mensajes** | Chat en tiempo real | `mensajes` | C R | `/api/mensajes` |
| **Calificaciones** | Rating de viajes | `calificaciones` | C R | `/api/calificaciones` |
| **Notificaciones** | Alertas del sistema | `notificaciones` | R U | `/api/notificaciones` |
| **Soporte** | Tickets de ayuda | `soporte_tickets` | C R | `/api/soporte` |

### 🔷 1. MÓDULO DE USUARIOS

#### Estructura de la Tabla

```sql
CREATE TABLE usuarios (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  telefono VARCHAR(20),
  password VARCHAR(255) NOT NULL,
  foto_perfil LONGTEXT,              -- Base64 de la imagen
  es_conductor BOOLEAN DEFAULT FALSE,
  ciudad VARCHAR(100),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Operaciones CRUD

| Operación | Método HTTP | Endpoint | Controlador | Vista |
|-----------|-------------|----------|-------------|-------|
| **Create** | POST | `/api/register` | `BaseDeDatos.register()` | [app/registro.tsx](app/registro.tsx) |
| **Read** | GET | `/api/usuarios/:id` | `BaseDeDatos.obtenerUsuario()` | [src/vistas/PerfilVista.tsx](src/vistas/PerfilVista.tsx) |
| **Update (Perfil)** | PUT | `/api/usuarios/:id` | `BaseDeDatos.actualizarPerfil()` | [src/vistas/PerfilVista.tsx](src/vistas/PerfilVista.tsx) |
| **Update (Password)** | PUT | `/api/usuarios/:id/password` | `BaseDeDatos.actualizarPassword()` | [src/vistas/ConfiguracionVista.tsx](src/vistas/ConfiguracionVista.tsx) |
| **Update (Foto)** | PUT | `/api/usuarios/:id/foto` | `BaseDeDatos.actualizarFoto()` | [src/vistas/PerfilVista.tsx](src/vistas/PerfilVista.tsx) |

#### Ejemplo: Actualizar Perfil

**Frontend (Controlador):**
```typescript
actualizarPerfil: async (usuario_id: number, data: {
  nombre: string;
  email: string;
  telefono: string;
  ciudad?: string;
}) => {
  try {
    const response = await fetch(`${API_URL}/usuarios/${usuario_id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(data),
    });
    return await response.json();
  } catch (error) {
    return { success: false, message: "Error de conexión" };
  }
}
```

**Backend (Express):**
```javascript
app.put("/api/usuarios/:id", (req, res) => {
  const { nombre, email, telefono, ciudad } = req.body;
  const query = `
    UPDATE usuarios 
    SET nombre = ?, email = ?, telefono = ?, ciudad = ? 
    WHERE id = ?
  `;
  
  db.execute(
    query,
    [nombre, email, telefono, ciudad, req.params.id],
    (err, result) => {
      if (err) return res.status(500).json({ error: err.message });
      res.json({ success: true, message: "Perfil actualizado" });
    }
  );
});
```

**Vista (PerfilVista):**
```typescript
const guardarCambios = async () => {
  const result = await BaseDeDatos.actualizarPerfil(user.id, {
    nombre: nombreEditado,
    email: emailEditado,
    telefono: telefonoEditado,
    ciudad: ciudadEditada
  });
  
  if (result.success) {
    await updateUser({ nombre: nombreEditado, email: emailEditado });
    Alert.alert("Éxito", "Perfil actualizado correctamente");
  } else {
    Alert.alert("Error", result.message);
  }
};
```

### 🔷 2. MÓDULO DE VIAJES

#### Estructura de la Tabla

```sql
CREATE TABLE viajes (
  id INT AUTO_INCREMENT PRIMARY KEY,
  usuario_id INT NOT NULL,
  conductor_id INT,
  origen VARCHAR(255) NOT NULL,
  destino VARCHAR(255) NOT NULL,
  precio_estimado DECIMAL(10, 2),
  precio_final DECIMAL(10, 2),
  tipo ENUM('viaje', 'envio', 'reserva', 'alquiler') DEFAULT 'viaje',
  estado ENUM('solicitado', 'aceptado', 'en_curso', 'completado', 'cancelado'),
  detalles TEXT,                      -- JSON con info adicional
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id),
  FOREIGN KEY (conductor_id) REFERENCES conductores(id)
);
```

#### Operaciones CRUD

| Operación | Endpoint | Descripción | Usuario |
|-----------|----------|-------------|---------|
| **Create** | POST `/api/viajes/solicitar` | Crear nueva solicitud de viaje | Pasajero |
| **Read (Historial)** | GET `/api/viajes/historial/:usuario_id` | Obtener viajes pasados del usuario | Pasajero |
| **Read (Pendientes)** | GET `/api/viajes/pendientes` | Viajes sin conductor asignado | Conductor |
| **Read (Activos)** | GET `/api/viajes/activos/:conductor_id` | Viajes en curso del conductor | Conductor |
| **Update (Estado)** | PUT `/api/viajes/:id/estado` | Cambiar estado del viaje | Ambos |
| **Update (Cancelar)** | PUT `/api/viajes/:id/cancelar` | Cancelar viaje | Ambos |

#### Flujo Completo de un Viaje

```
1. CREATE - Solicitar Viaje (Pasajero)
   ↓
   POST /api/viajes/solicitar
   {
     usuario_id: 1,
     origen: "Av. Cristo Redentor",
     destino: "Mall Ventura",
     precio_estimado: 15.00,
     tipo: "viaje"
   }
   ↓
   Backend busca conductor cercano (Fórmula Haversine)
   ↓
   INSERT INTO viajes + Asignar conductor_id
   ↓
   Response: { viaje_id: 25, conductor_id: 3, estado: "aceptado" }

2. READ - Ver Detalles (Ambos)
   ↓
   GET /api/viajes/25
   ↓
   SELECT con JOIN para obtener datos del pasajero y conductor

3. UPDATE - Cambiar Estado
   ↓
   PUT /api/viajes/25/estado
   { estado: "en_curso" }
   ↓
   UPDATE viajes SET estado = 'en_curso' WHERE id = 25

4. UPDATE - Completar Viaje
   ↓
   PUT /api/viajes/25/estado
   { estado: "completado", precio_final: 18.50 }
   ↓
   UPDATE + Notificar al pasajero

5. CREATE - Calificar
   ↓
   POST /api/calificaciones
   { viaje_id: 25, puntaje: 5, comentario: "Excelente servicio" }
```

#### Ejemplo: Solicitar Viaje

**Controlador (Frontend):**
```typescript
solicitarViaje: async (
  usuario_id: number,
  origen: string,
  destino: string,
  precio: number,
  tipo: "viaje" | "envio" | "reserva" | "alquiler" = "viaje",
  detalles: any = null,
  latitud?: number,
  longitud?: number
) => {
  try {
    const response = await fetch(`${API_URL}/viajes/solicitar`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        usuario_id,
        origen,
        destino,
        precio_estimado: precio,
        tipo,
        detalles: detalles ? JSON.stringify(detalles) : null,
        latitud,
        longitud
      })
    });
    return await response.json();
  } catch (error) {
    return { success: false, message: "Error de conexión" };
  }
}
```

**Backend:**
```javascript
app.post("/api/viajes/solicitar", (req, res) => {
  const { usuario_id, origen, destino, precio_estimado, tipo, detalles, latitud, longitud } = req.body;

  // Buscar conductor más cercano (Fórmula de Haversine)
  const findDriverQuery = `
    SELECT id, 
    (6371 * acos(cos(radians(?)) * cos(radians(latitud)) * 
     cos(radians(longitud) - radians(?)) + 
     sin(radians(?)) * sin(radians(latitud)))) AS distance 
    FROM conductores 
    WHERE estado = 'activo' 
    ORDER BY distance ASC 
    LIMIT 1
  `;

  db.execute(findDriverQuery, [latitud, longitud, latitud], (err, drivers) => {
    const conductor_id = drivers.length > 0 ? drivers[0].id : null;
    const estado = conductor_id ? "aceptado" : "solicitado";

    const insertQuery = `
      INSERT INTO viajes (usuario_id, conductor_id, origen, destino, precio_estimado, estado, tipo, detalles) 
      VALUES (?, ?, ?, ?, ?, ?, ?, ?)
    `;

    db.execute(
      insertQuery,
      [usuario_id, conductor_id, origen, destino, precio_estimado, estado, tipo, detalles],
      (err, result) => {
        if (err) return res.status(500).json({ error: err.message });
        res.json({
          success: true,
          viaje_id: result.insertId,
          conductor_id,
          message: conductor_id ? "Viaje asignado" : "Buscando conductor..."
        });
      }
    );
  });
});
```

### 🔷 3. MÓDULO DE MÉTODOS DE PAGO

#### Estructura de la Tabla

```sql
CREATE TABLE metodos_pago (
  id INT AUTO_INCREMENT PRIMARY KEY,
  usuario_id INT NOT NULL,
  tipo ENUM('tarjeta', 'efectivo', 'digital') DEFAULT 'tarjeta',
  marca VARCHAR(50),                  -- Visa, Mastercard, etc.
  ultimos_digitos VARCHAR(4),
  token_pago VARCHAR(255),           -- Token de pasarela de pago
  principal BOOLEAN DEFAULT FALSE,
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE CASCADE
);
```

#### Operaciones CRUD

| Operación | Método | Endpoint | Descripción |
|-----------|--------|----------|-------------|
| **Create** | POST | `/api/metodos_pago` | Agregar nueva tarjeta/método |
| **Read** | GET | `/api/metodos_pago/:usuario_id` | Listar métodos del usuario |
| **Delete** | DELETE | `/api/metodos_pago/:id` | Eliminar método de pago |

**Ejemplo: Agregar Método de Pago**

```typescript
// Frontend
const agregarTarjeta = async () => {
  const result = await BaseDeDatos.agregarMetodoPago({
    usuario_id: user.id,
    tipo: "tarjeta",
    marca: "Visa",
    ultimos_digitos: "4242",
    token_pago: "tok_visa_4242"  // Generado por Stripe/PayPal
  });
  
  if (result.success) {
    Alert.alert("Éxito", "Tarjeta agregada correctamente");
    cargarMetodos();
  }
};
```

### 🔷 4. MÓDULO DE LUGARES GUARDADOS

#### Estructura de la Tabla

```sql
CREATE TABLE lugares_guardados (
  id INT AUTO_INCREMENT PRIMARY KEY,
  usuario_id INT NOT NULL,
  nombre VARCHAR(50) NOT NULL,       -- "Casa", "Trabajo", "Gimnasio"
  direccion VARCHAR(255) NOT NULL,
  latitud DECIMAL(10, 8),
  longitud DECIMAL(11, 8),
  icono VARCHAR(50),
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE CASCADE
);
```

#### Operaciones CRUD

```typescript
// CREATE
await BaseDeDatos.guardarLugar(user.id, "Casa", "Av. Principal 123");

// READ
const lugares = await BaseDeDatos.obtenerLugares(user.id);

// DELETE
await BaseDeDatos.eliminarLugar(lugar_id);
```

### 🔷 5. MÓDULO DE MENSAJES (CHAT)

#### Estructura de la Tabla

```sql
CREATE TABLE mensajes (
  id INT AUTO_INCREMENT PRIMARY KEY,
  viaje_id INT NOT NULL,
  usuario_id INT NOT NULL,
  mensaje TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (viaje_id) REFERENCES viajes(id),
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);
```

#### Operaciones

```typescript
// CREATE - Enviar mensaje
await BaseDeDatos.enviarMensaje(viaje_id, user.id, "Ya estoy llegando");

// READ - Obtener historial
const mensajes = await BaseDeDatos.obtenerMensajes(viaje_id);
```

### 📊 Resumen de Endpoints RESTful

| Recurso | GET | POST | PUT | DELETE |
|---------|-----|------|-----|--------|
| `/api/usuarios` | ✅ Obtener perfil | ✅ Registro | ✅ Actualizar | ❌ |
| `/api/viajes` | ✅ Historial | ✅ Solicitar | ✅ Cambiar estado | ❌ |
| `/api/conductores` | ✅ Listar | ✅ Crear perfil | ✅ Estado | ❌ |
| `/api/metodos_pago` | ✅ Listar | ✅ Agregar | ❌ | ✅ Eliminar |
| `/api/lugares` | ✅ Listar | ✅ Guardar | ❌ | ✅ Eliminar |
| `/api/mensajes` | ✅ Historial | ✅ Enviar | ❌ | ❌ |
| `/api/calificaciones` | ✅ Ver | ✅ Calificar | ❌ | ❌ |
| `/api/notificaciones` | ✅ Obtener | ❌ | ✅ Marcar leída | ❌ |

---

## 6.7 GESTIÓN DE SESIONES DE USUARIO

La gestión de sesiones permite mantener al usuario autenticado a través de múltiples sesiones de la aplicación, garantizando que no tenga que iniciar sesión cada vez que abre la app. Se implementa mediante una combinación de Context API, AsyncStorage local y persistencia en base de datos, proporcionando una experiencia fluida y segura.

### 🔄 Componentes del Sistema de Sesiones

| Componente | Tecnología | Función | Persistencia |
|------------|------------|---------|--------------|
| **AuthContext** | React Context API | Estado global de autenticación | En memoria (volátil) |
| **AsyncStorage** | @react-native-async-storage | Almacenamiento local en dispositivo | Persistente |
| **Tabla sessions** | MySQL | Registro de sesiones activas (opcional) | Base de datos |
| **useAuth Hook** | Custom Hook | Interfaz para acceder al contexto | N/A |

### 📱 Ciclo de Vida de una Sesión

```
┌─────────────────────────────────────────────────────────────────┐
│                    INICIO DE LA APLICACIÓN                      │
└─────────────────────────────────────────────────────────────────┘

App se monta
    │
    ▼
┌───────────────────────────────────────┐
│ AuthProvider - useEffect()            │
│ Ejecuta checkUser()                   │
└───────────────┬───────────────────────┘
                │
                ▼
┌───────────────────────────────────────┐
│ AsyncStorage.getItem("user_session")  │
└───────────────┬───────────────────────┘
                │
        ┌───────┴───────┐
        │               │
        ▼               ▼
    Existe          No Existe
        │               │
        ▼               ▼
┌─────────────┐   ┌─────────────┐
│ Parse JSON  │   │ Mantener    │
│ Setear user │   │ user = null │
└──────┬──────┘   └──────┬──────┘
       │                 │
       ▼                 ▼
┌─────────────┐   ┌─────────────┐
│ isLoading = │   │ isLoading = │
│    false    │   │    false    │
└──────┬──────┘   └──────┬──────┘
       │                 │
       ▼                 ▼
┌─────────────┐   ┌─────────────┐
│ Navegar a   │   │ Mostrar     │
│ Dashboard   │   │ Login       │
└─────────────┘   └─────────────┘
```

### 💾 Persistencia con AsyncStorage

#### Implementación

```typescript
// 📍 Ubicación: src/context/AuthContext.tsx

// 1. GUARDAR SESIÓN (al hacer login o registro)
const login = async (email: string, password: string) => {
  try {
    const result = await BaseDeDatos.login(email, password);
    
    if (result.success && result.user) {
      // Guardar en estado
      setUser(result.user);
      
      // Persistir en AsyncStorage
      await AsyncStorage.setItem(
        "user_session",
        JSON.stringify(result.user)
      );
      
      return { success: true };
    }
  } catch (error) {
    return { success: false, message: "Error de conexión" };
  }
};

// 2. RECUPERAR SESIÓN (al iniciar la app)
const checkUser = async () => {
  try {
    const storedUser = await AsyncStorage.getItem("user_session");
    
    if (storedUser) {
      const userData = JSON.parse(storedUser);
      setUser(userData);
      console.log("Sesión restaurada:", userData.email);
    }
  } catch (error) {
    console.error("Error al recuperar sesión:", error);
  } finally {
    setIsLoading(false);  // Siempre terminar el loading
  }
};

// 3. ACTUALIZAR SESIÓN (al cambiar datos)
const updateUser = async (userData: Partial<User>) => {
  if (user) {
    const updatedUser = { ...user, ...userData };
    
    // Actualizar estado
    setUser(updatedUser);
    
    // Sincronizar AsyncStorage
    await AsyncStorage.setItem(
      "user_session",
      JSON.stringify(updatedUser)
    );
  }
};

// 4. ELIMINAR SESIÓN (al hacer logout)
const logout = async () => {
  // Limpiar estado
  setUser(null);
  
  // Eliminar de AsyncStorage
  await AsyncStorage.removeItem("user_session");
  
  // Opcional: Notificar al servidor
  // await BaseDeDatos.cerrarSesion(user.id);
};
```

### 🔐 Estructura de Datos de Sesión

#### Datos Almacenados

```typescript
// Formato en AsyncStorage
{
  "id": 1,
  "nombre": "Juan Pérez",
  "email": "juan@example.com",
  "telefono": "12345678",
  "es_conductor": 0,
  "foto_perfil": "data:image/jpeg;base64,/9j/4AAQ...",
  "conductor_id": null,
  "ciudad": "Santa Cruz"
}
```

#### Tabla de Propiedades

| Campo | Tipo | Descripción | ¿Sensible? |
|-------|------|-------------|-----------|
| `id` | number | ID único del usuario | No |
| `nombre` | string | Nombre completo | No |
| `email` | string | Correo electrónico | ⚠️ Sí |
| `telefono` | string | Número de teléfono | ⚠️ Sí |
| `es_conductor` | 0 \| 1 | Rol del usuario | No |
| `foto_perfil` | string (Base64) | Imagen de perfil | No |
| `conductor_id` | number \| null | ID de conductor (si aplica) | No |
| `ciudad` | string | Ciudad de residencia | No |

### 🛡️ Seguridad de Sesiones

#### Medidas Implementadas

| Medida | Implementación | Nivel |
|--------|----------------|-------|
| **Almacenamiento cifrado** | AsyncStorage sin cifrado adicional | ⚠️ Básico |
| **Validación de sesión** | Solo validación local | ⚠️ Básico |
| **Expiración** | No implementada | ❌ Ninguno |
| **Revocación remota** | No implementada | ❌ Ninguno |

#### Mejoras Recomendadas

```typescript
// ✅ 1. Cifrado con expo-secure-store
import * as SecureStore from 'expo-secure-store';

// Guardar
await SecureStore.setItemAsync('user_session', JSON.stringify(user));

// Recuperar
const session = await SecureStore.getItemAsync('user_session');

// ✅ 2. Token de sesión con expiración
const token = jwt.sign(
  { userId: user.id },
  SECRET_KEY,
  { expiresIn: '7d' }  // Expira en 7 días
);

// ✅ 3. Validación periódica con el servidor
const validateSession = async () => {
  const token = await AsyncStorage.getItem('token');
  const response = await fetch(`${API_URL}/validate-session`, {
    headers: { 'Authorization': `Bearer ${token}` }
  });
  
  if (!response.ok) {
    // Token inválido o expirado
    await logout();
  }
};

// ✅ 4. Tabla de sesiones en BD
CREATE TABLE sesiones (
  id INT AUTO_INCREMENT PRIMARY KEY,
  usuario_id INT NOT NULL,
  token VARCHAR(255) UNIQUE NOT NULL,
  dispositivo VARCHAR(100),
  ip_address VARCHAR(45),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  expires_at TIMESTAMP,
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE CASCADE
);
```

### 📊 Gestión de Múltiples Sesiones

#### Escenarios Comunes

| Escenario | Comportamiento Actual | Comportamiento Ideal |
|-----------|----------------------|----------------------|
| **Mismo usuario en 2 dispositivos** | Ambas sesiones activas independientes | Sincronización de datos |
| **Cambio de contraseña** | Sesiones anteriores siguen activas | Cerrar todas las sesiones |
| **Desinstalación de app** | Sesión persiste en AsyncStorage | Eliminar automáticamente |
| **Cierre forzado** | Sesión se mantiene | Mantener sesión |

#### Implementación de Múltiples Dispositivos

```javascript
// Backend: Tabla de sesiones
app.post("/api/login", (req, res) => {
  const { email, password, dispositivo } = req.body;
  
  // Validar credenciales...
  
  if (userValid) {
    // Generar token único
    const token = generateToken();
    
    // Registrar sesión
    db.execute(
      "INSERT INTO sesiones (usuario_id, token, dispositivo, expires_at) VALUES (?, ?, ?, DATE_ADD(NOW(), INTERVAL 7 DAY))",
      [user.id, token, dispositivo],
      (err) => {
        res.json({ success: true, token, user });
      }
    );
  }
});

// Cerrar sesión en dispositivo específico
app.post("/api/logout", (req, res) => {
  const { token } = req.body;
  
  db.execute(
    "DELETE FROM sesiones WHERE token = ?",
    [token],
    (err) => {
      res.json({ success: true });
    }
  );
});

// Cerrar todas las sesiones del usuario
app.post("/api/logout-all", (req, res) => {
  const { usuario_id } = req.body;
  
  db.execute(
    "DELETE FROM sesiones WHERE usuario_id = ?",
    [usuario_id],
    (err) => {
      res.json({ success: true, message: "Todas las sesiones cerradas" });
    }
  );
});
```

### 🔄 Sincronización de Datos entre Sesiones

```typescript
// Frontend: Polling para cambios
useEffect(() => {
  const interval = setInterval(async () => {
    if (user) {
      // Verificar si hay cambios en el perfil
      const updatedUser = await BaseDeDatos.obtenerUsuario(user.id);
      
      if (JSON.stringify(updatedUser) !== JSON.stringify(user)) {
        // Actualizar contexto y AsyncStorage
        await updateUser(updatedUser);
        Alert.alert("Actualización", "Tu perfil ha sido actualizado desde otro dispositivo");
      }
    }
  }, 30000);  // Cada 30 segundos
  
  return () => clearInterval(interval);
}, [user]);
```

### 📱 Uso del Hook useAuth en Componentes

```typescript
// Cualquier componente puede acceder a la sesión
import { useAuth } from '../context/AuthContext';

function PerfilVista() {
  const { user, logout, updateUser, isLoading } = useAuth();
  
  if (isLoading) {
    return <ActivityIndicator />;
  }
  
  if (!user) {
    return <Text>No has iniciado sesión</Text>;
  }
  
  return (
    <View>
      <Text>Bienvenido {user.nombre}</Text>
      <Text>Email: {user.email}</Text>
      
      <Button title="Cerrar Sesión" onPress={logout} />
    </View>
  );
}
```

### 🎯 Ventajas del Sistema Implementado

| Ventaja | Descripción | Beneficio |
|---------|-------------|-----------|
| **Persistencia** | Sesión se mantiene entre cierres de app | UX fluida, no re-login constante |
| **Sincronización** | Cambios se reflejan en AsyncStorage | Datos siempre actualizados |
| **Centralización** | Un solo punto de acceso (useAuth) | Código limpio y mantenible |
| **Validación automática** | CheckUser al iniciar app | Seguridad básica |
| **Logout universal** | Limpia estado y storage simultáneamente | Sin residuos de sesión |

### 🔍 Debugging de Sesiones

```typescript
// Utilidades para desarrollo
const SessionDebugger = () => {
  const { user } = useAuth();
  
  const verSesion = async () => {
    const stored = await AsyncStorage.getItem('user_session');
    console.log("Sesión en AsyncStorage:", stored);
    console.log("Sesión en Context:", user);
  };
  
  const limpiarSesion = async () => {
    await AsyncStorage.clear();
    console.log("AsyncStorage limpiado");
  };
  
  return (
    <View>
      <Button title="Ver Sesión" onPress={verSesion} />
      <Button title="Limpiar Sesión" onPress={limpiarSesion} />
    </View>
  );
};
```

---

## 🚀 Conclusiones

Las implementaciones de **autenticación**, **CRUD** y **gestión de sesiones** forman la columna vertebral de la aplicación GoRide. El sistema actual proporciona funcionalidad completa para operaciones básicas, con oportunidades claras de mejora en seguridad mediante tokens JWT, hashing de contraseñas y validación de sesiones en servidor.

### Próximos Pasos Recomendados

1. **Seguridad:** Implementar bcrypt + JWT
2. **Validación:** Middleware de autenticación en todas las rutas protegidas
3. **Monitoreo:** Logging de sesiones activas
4. **Optimización:** Caché de datos frecuentes
5. **Testing:** Unit tests para AuthContext y endpoints CRUD

---

**Documentación actualizada:** Enero 2026  
**Versión:** 1.0.0
