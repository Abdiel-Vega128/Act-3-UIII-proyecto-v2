# 📋 PLAN DE IMPLEMENTACIÓN ACTUALIZADO (CUMPLIENDO RESTRICCIONES)

## 🎯 OBJETIVO
Desarrollar una aplicación Flutter multiplataforma (Android, Web, Windows) para la gestión integral de un restaurante italiano, utilizando **configuración estándar de Firebase**, **excluyendo explícitamente el modo Producción** y **sin integrar Google Analytics**, manteniendo arquitectura limpia, Riverpod, GoRouter y CRUD completo para las 15 entidades del diagrama.

---

## ⚙️ FASE 0: CONFIGURACIÓN FIREBASE (RESTRICCIONES EXPLÍCITAS)
1. **Creación del proyecto en Firebase Console**
   - Nombre: `proyectorestaurante`
   - **Analíticas:** Desmarcar/Rechazar explícitamente "Google Analytics" durante la creación y en `Configuración del proyecto > Integraciones`
   - **Región:** Seleccionar región estándar más cercana (ej. `us-central` o `southamerica-east1`)
2. **Cloud Firestore**
   - **NO seleccionar "Modo de producción"**
   - Elegir configuración estándar de desarrollo (reglas abiertas por defecto para iteración rápida)
   - Crear índice compuesto solo si la UI lo requiere explícitamente
3. **Authentication**
   - Habilitar proveedores estándar: `Correo electrónico/Contraseña` y `Google`
   - Configurar dominios autorizados para Web y Windows (localhost + IPs de prueba)
4. **Vinculación Flutter**
   - Ejecutar `flutterfire configure` seleccionando solo Android, Web y Windows
   - Verificar generación de `firebase_options.dart` sin dependencias de analytics

---

## 🏗️ FASE 1: ARQUITECTURA Y BASE DEL PROYECTO
- Estructura por características: `lib/features/[entidad]/data|presentation` + `lib/core`
- Estado: `flutter_riverpod` | Rutas: `go_router` | UI: Material 3 + paleta italiana
- `pubspec.yaml` con dependencias mínimas necesarias (Firebase Core, Auth, Firestore, Riverpod, GoRouter, intl, uuid, google_fonts)
- **Excluir** cualquier paquete de telemetría, crashlytics o analytics

---

## 🔐 FASE 2: AUTENTICACIÓN Y CONTROL DE ACCESO
- Flujos estándar: Login, Registro, Recuperar contraseña, Google Sign-In, Cierre de sesión
- Persistencia de sesión nativa de Firebase Auth
- Middleware de navegación: redirección automática a `/login` si no hay usuario autenticado
- Verificación de rol (`admin` vs `empleado`) mediante lectura del documento en `empleados/{uid}`

---

## 📦 FASE 3: CRUD COMPLETO (15 ENTIDADES)
Desarrollo secuencial aplicando patrón uniforme:
`Modelo (Firestore ↔ Dart) → Repositorio (streams + operaciones) → Notifier (estados) → UI (lista + formulario + detalle)`

1. **Base:** `roles`, `empleados`, `clientes`, `mesas`
2. **Catálogo:** `categorias`, `platillos`, `ingredientes`, `proveedores`
3. **Operaciones:** `reservaciones`, `pedidos`, `detalle_pedido`, `pagos`
4. **Inventario/Producción:** `compras`, `detalle_compra`, `recetas`

Cada módulo incluye: validación en tiempo real, búsqueda/filtros, confirmación de eliminación, manejo de errores y estados vacíos/carga.

---

## 👑 FASE 4: PANEL DE ADMINISTRACIÓN Y RBAC
- Dashboard exclusivo para `id_rol == 'admin'`
- Métricas calculadas localmente desde streams de Firestore (ventas del día, pedidos activos, reservas, stock bajo)
- Accesos directos a los 15 CRUDs
- Gestión de empleados: activación/desactivación, asignación de rol, reset de contraseña
- Ocultamiento condicional de botones/módulos según rol (lógica en capa de presentación)

---

## 🔄 FASE 5: FLUJOS DE NEGOCIO INTEGRADOS
- Ciclo: Reserva → Asignación mesa → Pedido → Cocina → Pago
- Descuento atómico de ingredientes al confirmar pedido
- Alertas de stock mínimo y cálculo de costo de receta
- Exportación local a CSV/PDF sin servicios externos

---

## 📱 FASE 6: UI/UX RESPONSIVE MULTIPLATAFORMA
- Móvil: navegación inferior, listas verticales optimizadas
- Web/Desktop: sidebar, grids adaptables, atajos de teclado
- Componentes base: skeletons, estados vacíos, validaciones inline, snackbars contextuales
- Compatibilidad verificada en Android, Chrome/Edge y Windows 10/11

---

## ️ FASE 7: REGLAS ESTÁNDAR Y DESPLIEGUE (SIN MODO PRODUCCIÓN)
- Firestore mantiene configuración estándar de desarrollo según solicitud
- Reglas de seguridad básicas por colección (lectura/escritura autenticada, validación de tipos)
- Builds: `flutter build apk`, `flutter build web`, `flutter build windows`
- Documentación de ejecución local y requisitos mínimos por plataforma

---

##  FASE 8: CONTROL DE VERSIONES Y GITHUB
- Ramas: `main`, `develop`, `feature/*`
- Script `enviargithub.dart` en raíz: automatiza `git init`, `add`, commit interactivo y `push`
- README con: configuración Firebase estándar, comandos de ejecución, estructura y guía de roles

---

## ✅ CHECKLIST DE CUMPLIMIENTO
- [x] Firebase creado **sin Google Analytics**
- [x] Firestore configurado en modo estándar **NO producción**
- [x] Arquitectura por features + Riverpod + GoRouter
- [x] 15 CRUDs completos con patrón consistente
- [x] Panel Admin exclusivo con RBAC en capa UI
- [x] UI responsive Android/Web/Windows
- [x] Reglas estándar de seguridad (sin bloqueo production-mode)
- [x] Script `enviargithub.dart` funcional
- [x] Cero dependencias de analíticas o telemetría

Prompt:
Crea una aplicación Flutter multiplataforma (Android, Web y Windows) llamada "ProyectoRestaurante" para la gestión integral de un restaurante italiano, utilizando Firebase Authentication (Email/Password y Google Sign-In) y Cloud Firestore como backend con configuración estándar, descartando explícitamente Google Analytics y manteniendo Firestore en modo de prueba/desarrollo (sin activar modo producción). La app debe implementar un sistema de control de acceso por roles (RBAC) con un panel de administrador exclusivo, navegación segura con GoRouter, manejo de estado con Riverpod y una arquitectura basada en características (`lib/features/[entidad]/data|presentation` + `lib/core`). Desarrolla el CRUD completo para las 15 entidades exactas del diagrama: roles, empleados, clientes, mesas, categorias, platillos, ingredientes, proveedores, reservaciones, pedidos, detalle_pedido, pagos, compras, detalle_compra y recetas, incluyendo modelos con serialización Firestore, repositorios con streams, providers/notifiers, pantallas de listado con filtros/búsqueda, formularios con validación y widgets reutilizables. Aplica una interfaz moderna con tema Material 3 y paleta italiana, entrega el código completo listo para ejecutar, las dependencias en `pubspec.yaml`, instrucciones de configuración Firebase estándar y un script ejecutable `enviargithub.dart` en la raíz que automatice la inicialización del repositorio Git y la subida a GitHub.
