# Sistema Nacional de Vacunación (VacunaRD)

Frontend desarrollado con **React 19 + Vite 8**, arquitectura de Atomic Design y un sistema de enrutamiento mediante `RouteProvider`.

Para una explicación exhaustiva de la arquitectura y funcionamiento de todo el código, consulte el archivo:
👉 [**DOCUMENTACION_SISTEMA.md**](file:///c:/Users/cp932/OneDrive/Documents/proyectos/sistema-de-vacunacion-nacional-main/DOCUMENTACION_SISTEMA.md)

---

## Módulos y Vistas Implementadas

1. **Página de Inicio (`#/`)**: Presentación del sistema de vacunación con métricas nacionales, catálogo de vacunas disponibles y navegación superior persistente.
2. **Login (`#/login`)**:
   - Pestaña **Ciudadano**: Acceso para consultar la cartilla digital.
   - Pestaña **Administrador**: Acceso exclusivo restringido que requiere **código de seguridad institucional** (`ADMIN-VAC-2026`) y contraseña administrativa.
3. **Registro Ciudadano (`#/register`)**: Formulario oficial con autocompletado y validaciones de cédula dominicana, teléfono, correo y contraseña.
4. **Panel de Control Administrador CRUD (`#/admin`)**:
   - **Solo accesible con el código específico y credenciales de administrador en el login**.
   - Gestión integral de usuarios (Crear nuevo usuario, Consultar padrón con búsqueda en tiempo real y filtros, Editar información, Eliminar con confirmación y protección anti auto-borrado).
   - Gestión de cartillas y registro directo de nuevas dosis aplicadas a pacientes.
5. **Panel de Información de Usuario (`#/user`)**:
   - Carnet Digital de Vacunación con código QR SVG criptográfico de validación.
   - Cronología de vacunas aplicadas con centros de salud y médicos tratantes.
   - Función para imprimir/exportar constancia y actualizar datos de contacto.
6. **Librería de Íconos SVG (Sin Emojis)**: Todos los componentes utilizan íconos SVG nativos y escalables (`src/components/atoms/Icons.jsx`).

---

## Credenciales de Acceso Rápido

### Administrador (Panel de Control CRUD)
- **Correo**: `admin@vacunard.do`
- **Contraseña**: `admin123`
- **Código Institucional**: `ADMIN-VAC-2026`

### Ciudadano (Cartilla Digital)
- **Correo**: `maria@correo.do`
- **Contraseña**: `123456`

---

## Comandos del Proyecto

```bash
# Servidor de desarrollo
npm run dev

# Compilar para producción
npm run build

# Análisis de linting
npm run lint
```
