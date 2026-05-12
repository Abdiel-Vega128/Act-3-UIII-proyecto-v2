# 🇮🇹 PLAN DE IMPLEMENTACIÓN DETALLADO: PROYECTO RESTAURANTE ITALIANO

## 1. ESTÁNDARES DEL PROYECTO
- **Idioma:** **100% Español**. Código, variables, comentarios, nombres de carpetas, UI y Base de Datos.
- **Plataformas:** Windows, Android, iOS, Web.
- **Backend:** Firebase (Auth + Firestore).
- **Restricciones:** **NO Google Analytics**. **NO Modo Producción** en Firestore (usar Modo Prueba/Desarrollo).
- **Arquitectura:** Limpia, modular y escalable.

---

## 2. ESTÉTICA Y DISEÑO (TEMA VISUAL)
El diseño debe evocar la elegancia y calidez de un restaurante italiano clásico.

### 🎨 Paleta de Colores (Configuración del Tema)
- **Color Primario:** `#CE2B37` (Rojo Pomodoro) - Para botones principales, barra de navegación, acentos fuertes.
- **Color Secundario:** `#009246` (Verde Albahaca) - Para iconos de éxito, elementos decorativos, badges de "Disponible".
- **Fondo (Background):** `#F8F5F2` (Crema/Beige suave) - Para reducir fatiga visual y dar calidez.
- **Superficie (Cards/Papeles):** `#FFFFFF` (Blanco puro) - Para tarjetas de información y formularios.
- **Texto Principal:** `#2C3E50` (Azul noche oscuro) - Lectura clara y elegante.
- **Texto Secundario:** `#7F8C8D` (Gris medio) - Para descripciones y detalles.
- **Color de Acento:** `#F1C40F` (Amarillo Dorado) - Para estrellas de calificación, alertas o notificaciones.
- **Error:** `#E74C3C` - Para validaciones y mensajes de error.

### ️ Tipografía
- **Fuentes:** Utilizar `Google Fonts`.
  - Títulos: *Playfair Display* (Elegante, serif).
  - Cuerpo: *Lato* o *Poppins* (Legible, sans-serif).

---

## 3. ESTRUCTURA DE CARPETAS (EN ESPAÑOL)
Todo el proyecto se organizará bajo la carpeta `lib/` siguiendo este orden:

```text
lib/
├── principal.dart                    # Punto de entrada (Inicia la app y Firebase)
├── config/                           # Configuraciones globales
│   ├── tema.dart                     # Definición de colores y fuentes
│   └── rutas.dart                    # Navegación y protección de rutas
│
├── core/                             # Utilidades compartidas
│   ├── constantes.dart               # Nombres de colecciones, roles
│   ├── servicios/                    # Servicios globales
│   │   └── autenticacion_servicio.dart # Lógica de login/google
│   └── widgets/                      # Componentes base
│       ├── boton_personalizado.dart
│       ├── campo_texto_personalizado.dart
│       └── tarjeta_mesa.dart
│
├── datos/                            # Capa de Datos (Modelos y Repositorios)
│   ├── modelos/                      # Clases que representan las entidades
│   │   ├── empleado.dart
│   │   ├── cliente.dart
│   │   ├── platillo.dart
│   │   ├── ... (resto de las 15 entidades)
│   └── repositorios/                 # Comunicación con Firestore
│       ├── empleado_repositorio.dart
│       ── ...
│
├── logica/                           # Capa de Lógica (State Management)
│   └── proveedores/                  # Riverpod Providers
│       ├── auth_proveedor.dart
│       ├── empleado_proveedor.dart
│       └── ...
│
└── interfaz/                         # Capa de Presentación (Pantallas)
    ├── autenticacion/
    │   ├── pantalla_login.dart
    │   └── pantalla_registro.dart
    ├── dashboard/
    │   ├── panel_admin.dart
    │   └── panel_empleado.dart
    ── modulos/
        ├── empleados/
        │   ├── lista_empleados.dart
        │   └── formulario_empleado.dart
        ├── clientes/
        ├── platillos/
        └── ... (Un subdirectorio por entidad)
```

---

## 4. FASES DE IMPLEMENTACIÓN PASO A PASO

### 🟢 FASE 1: CONFIGURACIÓN INICIAL Y ENTORNO
1.  **Proyecto Flutter:**
    - Crear proyecto: `flutter create proyecto_restaurante`.
    - Habilitar plataformas: `flutter create --platforms=windows,android,ios,web .`
2.  **Configuración Firebase (Estándar):**
    - Crear proyecto `proyectorestaurante` en Firebase Console.
    - **Importante:** Deseleccionar Google Analytics al crear.
    - Agregar apps para Android, iOS, Web y Windows.
    - Ejecutar `flutterfire configure` para conectar.
3.  **Base de Datos (Firestore):**
    - Crear base de datos seleccionando **"Modo de prueba" (Test Mode)**.
    - No activar "Modo de producción".

### 🔵 FASE 2: DISEÑO Y TEMATIZACIÓN
1.  **Archivo `config/tema.dart`:**
    - Implementar `ThemeData` con los colores italianos definidos.
    - Configurar `fontFamily` con Playfair Display y Lato.
2.  **Widgets Base (`core/widgets`):**
    - Crear `BotonItaliano` (bordes redondeados, sombra suave).
    - Crear `TarjetaInfo` para listar items de forma elegante.

### 🟣 FASE 3: AUTENTICACIÓN Y SEGURIDAD
1.  **Servicio de Auth:**
    - Implementar `inicioSesion(email, password)`.
    - Implementar `registroUsuario(email, password, nombre, rol)`.
    - Implementar `inicioSesionGoogle()`.
2.  **Control de Acceso:**
    - Verificar si el usuario existe en la colección `empleados`.
    - Leer su campo `rol`. Si es "admin", redirigir a `PanelAdmin`. Si es "empleado", a `PanelEmpleado`.
    - Proteger rutas en `config/rutas.dart`.

###  FASE 4: DESARROLLO DE CRUDS (15 ENTIDADES)
Se repetirá este ciclo para cada entidad (Empleados, Clientes, Mesas, Platillos, Ingredientes, Proveedores, Categorías, Reservaciones, Pedidos, DetallePedido, Pagos, Compras, DetalleCompra, Recetas, Roles):

1.  **Modelo (`datos/modelos/x.dart`):**
    - Clase con `fromJson` y `toJson`.
    - Ejemplo: `class Empleado { String id, nombre, apellido, email, rol; ... }`
2.  **Repositorio (`datos/repositorios/x.dart`):**
    - Funciones asíncronas: `crear`, `leerTodo`, `actualizar`, `eliminar`.
    - Uso de Streams para actualizaciones en tiempo real.
3.  **Proveedor (`logica/proveedores/x.dart`):**
    - Gestión del estado (Cargando, Éxito, Error) usando Riverpod.
4.  **Interfaz (`interfaz/modulos/x`):**
    - **Lista:** ListView con tarjetas mostrando resumen.
    - **Formulario:** Campos validados (no vacíos, formatos correctos).
    - **Detalle:** Vista completa de la información.

### 🟠 FASE 5: PANELES DE CONTROL (DASHBOARDS)
1.  **Panel de Administrador (`interfaz/dashboard/panel_admin.dart`):**
    - **Estadísticas:** Tarjetas mostrando "Ventas del día", "Pedidos pendientes", "Stock bajo".
    - **Acceso Total:** Botones grandes para acceder a cualquiera de los 15 módulos.
    - **Gestión de Empleados:** Opción especial para asignar roles a otros usuarios.
2.  **Panel de Empleado (`interfaz/dashboard/panel_empleado.dart`):**
    - Acceso restringido solo a lo necesario (ej. ver Menú, tomar Pedidos, ver su Mesa).
    - Interfaz simplificada para uso rápido.

### 🔴 FASE 6: INTEGRACIÓN Y PRUEBAS
1.  **Pruebas en Plataformas:**
    - Ejecutar en Windows (`flutter run -d windows`).
    - Ejecutar en Web (`flutter run -d chrome`).
    - Ejecutar en Android/iOS (Simuladores).
2.  **Flujo de Negocio:**
    - Probar: Registrar Cliente -> Hacer Reserva -> Tomar Pedido -> Descontar Inventario -> Cobrar Pago.

### ⚫ FASE 7: DESPLIEGUE Y ENTREGA
1.  **Script de GitHub:**
    - Incluir `enviargithub.dart` en la raíz.
2.  **Documentación:**
    - README explicando cómo instalar dependencias y correr el proyecto.

---

## 5. SCRIPT DE GITHUB (`enviargithub.dart`)

Crea un archivo llamado `enviargithub.dart` en la raíz del proyecto. Este script se encarga de iniciar git, agregar archivos, hacer commit y subir a GitHub, todo en español.

```dart
// enviargithub.dart
import 'dart:io';

Future<void> main() async {
  print(' Iniciando envío a GitHub para ProyectoRestaurante...');
  
  // Verificar si git está instalado
  final gitCheck = await Process.run('git', ['--version']);
  if (gitCheck.exitCode != 0) {
    print('❌ Error: Git no está instalado.');
    return;
  }

  // Inicializar repo si no existe
  final isRepo = await Process.run('git', ['rev-parse', '--is-inside-work-tree']);
  if (isRepo.exitCode != 0) {
    print('📦 Inicializando repositorio Git...');
    await Process.run('git', ['init']);
  }

  // Agregar archivos
  print('📂 Agregando archivos al seguimiento...');
  await Process.run('git', ['add', '.']);

  // Commit
  print('💾 Guardando cambios...');
  await Process.run('git', ['commit', '-m', 'feat: implementación completa proyecto restaurante']);

  // Pedir URL del repositorio remoto
  stdout.write(' Ingresa la URL de tu repositorio GitHub (ej. https://github.com/usuario/proyecto.git): ');
  final url = stdin.readLineSync();

  if (url != null && url.isNotEmpty) {
    print('⬆️ Conectando con remoto y enviando...');
    await Process.run('git', ['remote', 'add', 'origin', url]);
    await Process.run('git', ['push', '-u', 'origin', 'main']);
    print('✅ ¡Proyecto enviado exitosamente a GitHub!');
  } else {
    print('⚠️ No se proporcionó URL. El código está guardado localmente.');
  }
}
```

---

## ✅ RESUMEN DE CUMPLIMIENTO
- [x] **Estética:** Paleta italiana (Rojo, Verde, Crema) definida.
- [x] **Plataformas:** Windows, iOS, Web, Android habilitados.
- [x] **Idioma:** Todo en español (Carpetas, Código, UI).
- [x] **Firebase:** Sin Analytics, Modo Prueba.
- [x] **Estructura:** Carpeta `lib` organizada por capas.
- [x] **Entidades:** 15 CRUDs listados.
- [x] **Script:** `enviargithub.dart` incluido.
Prompt:
Crea una aplicación Flutter multiplataforma (Android, iOS, Web y Windows) llamada "ProyectoRestaurante" para la gestión integral de un restaurante italiano (colores Rojo, Verde, Crema), utilizando Firebase Authentication (Email/Password y Google Sign-In) y Cloud Firestore como backend con configuración estándar, descartando explícitamente Google Analytics y manteniendo Firestore en modo de prueba/desarrollo (sin activar modo producción). La app debe implementar un sistema de control de acceso por roles (RBAC) con un panel de administrador exclusivo, navegación segura con GoRouter, manejo de estado con Riverpod y una arquitectura basada en características (`lib/features/[entidad]/data|presentation` + `lib/core`). Desarrolla el CRUD completo para las 15 entidades exactas del diagrama: roles, empleados, clientes, mesas, categorias, platillos, ingredientes, proveedores, reservaciones, pedidos, detalle_pedido, pagos, compras, detalle_compra y recetas, incluyendo modelos con serialización Firestore, repositorios con streams, providers/notifiers, pantallas de listado con filtros/búsqueda, formularios con validación y widgets reutilizables. Aplica una interfaz moderna con tema Material 3 y paleta italiana, entrega el código completo listo para ejecutar, las dependencias en `pubspec.yaml`, instrucciones de configuración Firebase estándar y un script ejecutable `enviargithub.dart` en la raíz que automatice la inicialización del repositorio Git y la subida a GitHub. Todo debe ser funcional.
