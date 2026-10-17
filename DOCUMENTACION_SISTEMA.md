# Sistema Nacional de Vacunación (VacunaRD)
## Documentación Técnica y Guía de Funcionamiento

Este documento detalla la arquitectura, el funcionamiento interno, el sistema de enrutamiento mediante `RouteProvider`, la seguridad institucional para el acceso administrativo, la gestión CRUD de usuarios y la cartilla digital ciudadana.

---

## 1. Arquitectura General y Tecnologías

El sistema está construido como una Single Page Application (SPA) modular bajo los principios de **Atomic Design** y **React 19**:

- **Core**: React 19 + Vite 8
- **Enrutamiento**: `RouteProvider` y `RouteContext` nativo con sincronización bidireccional de historial y hash (`window.location.hash`), permitiendo navegación rápida sin recargas, uso de botones atrás/adelante del navegador y marcadores directos.
- **Estado Global y Persistencia**: `AuthContext` con sincronización en tiempo real con `localStorage` para preservar usuarios registrados, sesiones y registros de vacunación.
- **Sistema de Iconografía**: 100% SVG nativo vectorial escalable en `Icons.jsx`, eliminando todos los emojis del código fuente.
- **Diseño Visual**: Estética moderna con modo oscuro, degradados dinámicos, bordes de acento, tipografía Inter y microinteracciones de interfaz de usuario.

---

## 2. Sistema de Enrutamiento con `RouteProvider`

El archivo [`src/context/RouteContext.jsx`](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/src/context/RouteContext.jsx) provee el contexto y los componentes del enrutador:

### Estructura de Rutas
| Ruta | Vista | Acceso / Guard |
| :--- | :--- | :--- |
| `#/` | `HomePage` | Acceso público con estadísticas y presentación nacional |
| `#/login` | `LoginPage` | Acceso ciudadano y acceso administrativo especial |
| `#/register` | `RegisterPage` | Registro de nuevos ciudadanos con validación |
| `#/admin` | `AdminPage` | Panel de control CRUD (protegido por código y credenciales) |
| `#/user` | `UserPanelPage` | Carnet digital ciudadano e historial de dosis |

### Elementos Exportados
- `<RouteProvider defaultRoute="/">`: Envuelve la aplicación en [`src/App.jsx`](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/src/App.jsx). Escucha eventos `hashchange` y despacha la ruta correspondiente.
- `useRoute()`: Hook para consumir `{ currentRoute, navigate, routeParams, ROUTES }`.
- `<RouteLink to="...">`: Enlace accesible que previene la recarga del navegador y ejecuta transiciones suaves con scroll al inicio.

---

## 3. Flujo de Autenticación y Código Administrativo Especial

El sistema implementa dos modalidades de acceso en [`src/components/pages/LoginPage.jsx`](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/src/components/pages/LoginPage.jsx):

### 3.1. Acceso Ciudadano
- **Campos**: Correo Electrónico y Contraseña.
- **Destino**: Redirige al Panel de Información de Usuario (`#/user`).
- **Demo rápido**: Botón chip para autocompletar credenciales de prueba (`maria@correo.do` / `123456`).

### 3.2. Acceso Administrativo Especial (Código + Contraseña)
Requerimiento de seguridad institucional:
1. **Correo Administrador**: Correo con rol `admin` (`admin@vacunard.do`).
2. **Contraseña**: Contraseña del administrador (`admin123`).
3. **Código de Seguridad Institucional**: `ADMIN-VAC-2026`.
   - Si el código no coincide exactamente, el sistema rechaza el intento mostrando el mensaje:
     > *"Código de autorización inválido. Ingrese el código oficial: ADMIN-VAC-2026"*
   - Al validarse, se establece `isAdminAuthenticated = true` y se concede acceso al Panel de Control (`#/admin`).
- **Guardia de Protección**: Si un usuario intenta acceder directamente a `#/admin` sin haberse autenticado con el código especial y su contraseña, [`AdminPage.jsx`](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/src/components/pages/AdminPage.jsx) bloquea el contenido y muestra una pantalla de **Área Restringida** con un enlace directo al inicio de sesión institucional.

---

## 4. Panel de Control Administrador (CRUD de Usuarios y Dosis)

Ubicado en [`src/components/pages/AdminPage.jsx`](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/src/components/pages/AdminPage.jsx):

### 4.1. Operaciones CRUD sobre Usuarios
- **Create (Crear)**:
  - Botón *"Nuevo Usuario / Paciente"* abre un modal con formulario completo.
  - Campos: Nombre Completo, Cédula de Identidad, Correo, Teléfono, Fecha de Nacimiento, Contraseña y selección de Rol (`Ciudadano` o `Administrador`).
  - Validación de colisión de correo y cédula duplicada.
- **Read (Leer / Consultar)**:
  - Tabla interactiva con padrón de usuarios: Avatar con iniciales, Nombre, ID, Cédula formateada, Correo, Teléfono, Fecha de nacimiento, Contador de dosis aplicadas, Rol y Fecha de registro.
  - Barra de búsqueda en tiempo real (filtra simultáneamente por nombre, cédula, correo o teléfono).
  - Pestañas de filtrado rápido por rol: *Todos*, *Ciudadanos*, *Administradores*.
- **Update (Actualizar)**:
  - Botón de edición por fila (ícono SVG `IconEdit`).
  - Permite modificar Nombre, Cédula, Fecha de nacimiento, Correo, Teléfono y Rol.
  - Bloqueo de seguridad: No permite que el administrador activo degrade su propio rol mientras esté en sesión.
- **Delete (Eliminar)**:
  - Botón de eliminación por fila (ícono SVG `IconTrash`).
  - Modal de confirmación con advertencia de irreversibilidad.
  - Protección de integridad: Bloquea la auto-eliminación de la cuenta con la que el administrador está logueado.

### 4.2. Gestión de Dosis y Cartilla desde el Panel de Administración
- Al hacer clic en el botón de cartilla (ícono SVG `IconClipboard` o badge de dosis) de cualquier usuario:
  - Se abre el modal con el desglose de todas las dosis aplicadas al paciente.
  - El administrador puede presionar *"Aplicar / Registrar Nueva Dosis"*:
    - Selecciona la vacuna del catálogo (COVID-19, Influenza, SRP, Hepatitis B, etc.).
    - Asigna el número de dosis (1ra, 2da, 3ra, etc.).
    - Define la fecha, centro de salud, médico responsable y notas/lote.
  - Permite remover dosis registradas erróneamente con botón SVG.

---

## 5. Panel de Información de Usuario (`UserPanelPage.jsx`)

Ubicado en [`src/components/pages/UserPanelPage.jsx`](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/src/components/pages/UserPanelPage.jsx):

1. **Carnet Digital de Vacunación**:
   - Encabezado oficial con escudo/emblema SVG del Ministerio de Salud Pública.
   - Código único de carnet (ej. `RD-VAC-000002`).
   - Avatar dinámico con iniciales del ciudadano.
   - Datos personales: Cédula, Edad calculada automáticamente a partir de la fecha de nacimiento, Teléfono y Correo.
2. **Código QR SVG Criptográfico de Verificación**:
   - Componente visual interactivo y escalable que certifica la autenticidad del documento.
   - Cuenta con interacción de zoom/ampliación al hacer clic.
3. **Métricas Personales de Inmunización**:
   - Total de dosis recibidas.
   - Esquemas completos alcanzados.
   - Variedad de vacunas aplicadas.
4. **Cronología de Vacunación (Timeline)**:
   - Tarjetas conectadas por línea de tiempo que muestran nombre de la vacuna, dosis (ej. *Dosis 2 de 3*), laboratorio, fecha, centro de salud, profesional responsable y notas.
5. **Funcionalidades Adicionales**:
   - **Imprimir Carnet**: Configurado con hoja de estilos `@media print` para generar un comprobante oficial de vacunación limpio en blanco y negro para trámites o viajes.
   - **Actualizar Datos**: Modal para editar teléfono y correo electrónico.
   - **Cerrar Sesión**: Botón con ícono SVG para salir de la sesión de manera segura.

---

## 6. Registro Ciudadano (`RegisterPage.jsx`)

Ubicado en [`src/components/pages/RegisterPage.jsx`](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/src/components/pages/RegisterPage.jsx):
- Formateo inteligente en tiempo real para **Cédula de Identidad** (`001-XXXXXXX-X`) y **Teléfono** (`809-XXX-XXXX`).
- Validación de contraseñas (longitud mínima y comprobación de coincidencia).
- Al completar el registro, crea el nuevo perfil ciudadano, inicia sesión automáticamente y redirige al usuario a su cartilla digital personal (`#/user`).

---

## 7. Librería de Íconos SVG (Reemplazo Total de Emojis)

El archivo [`src/components/atoms/Icons.jsx`](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/src/components/atoms/Icons.jsx) centraliza todos los íconos vectoriales del sistema construidos en SVG puro, con propiedades configurables de tamaño (`size`), clase CSS y color heredado (`stroke="currentColor"`):

- `IconSyringe`: Símbolo de vacunación y dosis.
- `IconShield`: Protección inmunológica y seguridad institucional.
- `IconHospital`: Centros médicos y policlínicas.
- `IconClock`: Tiempos de espera y recordatorios.
- `IconKey`: Código institucional de administración.
- `IconQrCode`: Código QR oficial del carnet digital.
- `IconPrinter`: Impresión y descarga de certificados.
- `IconUser`, `IconUsers`: Usuarios y padrón poblacional.
- `IconPlus`, `IconEdit`, `IconTrash`: Operaciones CRUD.
- `IconCheck`, `IconCheckCircle`, `IconAlertCircle`: Estados y validaciones.

---

## 8. Credenciales de Prueba

Para evaluar inmediatamente las distintas funciones del sistema:

### Administrador (Acceso a Panel CRUD)
- **Ruta**: `#/login` (Pestaña *Administrador*)
- **Correo**: `admin@vacunard.do`
- **Contraseña**: `admin123`
- **Código Institucional**: `ADMIN-VAC-2026`
*(Se incluye botón en pantalla para autocompletar estos datos en 1 clic)*

### Ciudadano (Acceso a Cartilla Digital)
- **Ruta**: `#/login` (Pestaña *Ciudadano*)
- **Correo**: `maria@correo.do`
- **Contraseña**: `123456`
*(Se incluye botón en pantalla para autocompletar estos datos en 1 clic)*

---

## 9. Comandos para Ejecutar el Proyecto

```bash
# Instalar dependencias (si no se han instalado)
npm install

# Iniciar servidor de desarrollo en Vite
npm run dev

# Construir versión de producción optimizada
npm run build

# Ejecutar análisis de linting
npm run lint
```
El servidor estará accesible en `http://localhost:5173/`.
