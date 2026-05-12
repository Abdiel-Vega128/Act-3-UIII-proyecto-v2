# 📋 PLAN DE IMPLEMENTACIÓN DETALLADO (SIN CÓDIGO)

## 🎯 OBJETIVO
Desarrollar una aplicación multiplataforma (Android, Web, Windows) en Flutter para la gestión integral de un restaurante italiano, con autenticación Firebase, control de roles, panel de administrador exclusivo y CRUD completo para 15 entidades, siguiendo arquitectura limpia y gestión de estado moderna.

---

## ️ FASE 0: PREPARACIÓN DEL ENTORNO
1. **Instalación de herramientas base**
   - Flutter SDK (versión estable 3.x o superior)
   - Dart SDK
   - Git
   - IDE (VS Code o Android Studio)
   - Extensiones: Flutter, Dart, GitLens, Firebase
2. **Configuración de cuenta Firebase**
   - Crear proyecto `proyectorestaurante`
   - Habilitar Authentication (Email/Password + Google)
   - Crear Firestore Database (modo prueba inicial)
   - Configurar Storage para imágenes (opcional para logos/fotos de empleados)
3. **Preparación del repositorio local**
   - Crear carpeta raíz del proyecto
   - Inicializar estructura base con Flutter CLI
   - Configurar `.gitignore` oficial de Flutter
   - Validar que `flutter doctor` no muestre errores críticos

---

## 🏗️ FASE 1: ARQUITECTURA Y ESTRUCTURA BASE
1. **Definición de arquitectura**
   - Implementar estructura basada en características (`lib/features/`)
   - Separar capas: `data`, `domain`, `presentation`
   - Crear carpeta `core/` para constantes, tema, utilidades y errores
2. **Configuración de dependencias**
   - Firebase Core, Auth, Firestore
   - State Management (Riverpod)
   - Routing (GoRouter)
   - Utilidades: intl, uuid, google_fonts, formatters
3. **Tematización y diseño base**
   - Definir paleta de colores italiana (verde, rojo, blanco, crema)
   - Configurar tipografía, bordes, sombras y espaciados globales
   - Crear componentes base: botones, campos de texto, tarjetas, diálogos
4. **Validación de estructura**
   - Verificar que `flutter analyze` no arroje warnings
   - Ejecutar `flutter build` para cada plataforma objetivo
   - Confirmar que la navegación base funciona

---

## 🔐 FASE 2: FIREBASE Y AUTENTICACIÓN
1. **Integración Firebase CLI**
   - Ejecutar `flutterfire configure` para las 3 plataformas
   - Verificar generación de `firebase_options.dart`
2. **Módulo de Autenticación**
   - Implementar proveedores de estado para sesión
   - Crear flujos: Login, Registro, Recuperación de contraseña
   - Integrar Google Sign-In con redirección segura
   - Persistir sesión y manejar reconexión automática
3. **Seguridad inicial**
   - Configurar reglas básicas de Firestore
   - Implementar middleware de verificación de autenticación
   - Proteger rutas privadas desde el router

---

## 📦 FASE 3: DESARROLLO DE MÓDULOS CRUD (PRIORIZADO)
*Se desarrollarán en orden de dependencia lógica:*

### 3.1 Entidades Base (Sin dependencias complejas)
- **Roles**: Definición de permisos y tipos de usuario
- **Empleados**: Vinculación a roles, gestión de estado laboral
- **Clientes**: Registro básico y historial
- **Mesas**: Capacidad, ubicación y estado

### 3.2 Catálogo y Menú
- **Categorías**: Clasificación de platillos
- **Platillos**: Información, precios, disponibilidad, imágenes
- **Ingredientes**: Inventario base, unidades de medida, stock
- **Proveedores**: Datos de contacto y relación con insumos

### 3.3 Operaciones Diarias
- **Reservaciones**: Fecha, hora, mesa asignada, cliente, estado
- **Pedidos**: Creación, asignación a mesa/empleado, estado del flujo
- **Detalle Pedido**: Relación platillo-cantidad-instrucciones
- **Pagos**: Métodos, montos, estado de transacción, vinculación a pedido

### 3.4 Inventario y Producción
- **Compras**: Órdenes a proveedores, fechas, totales
- **Detalle Compra**: Insumos adquiridos, cantidades, precios unitarios
- **Recetas**: Relación platillo-ingredientes, cantidades requeridas, costos

*Cada CRUD seguirá el mismo patrón:*
- Modelo con serialización Firestore
- Repositorio con operaciones asíncronas
- Provider/Notifier con manejo de estados (loading, data, error)
- Pantalla de listado con búsqueda/filtros
- Formulario de creación/edición con validación
- Diálogos de confirmación para eliminación
- Indicadores visuales de estado (activo/inactivo, pendiente/completado)

---

## 👑 FASE 4: PANEL DE ADMINISTRACIÓN Y CONTROL DE ROLES
1. **Sistema RBAC (Role-Based Access Control)**
   - Implementar verificación de rol en cada navegación
   - Crear reglas de Firestore dinámicas según rol
   - Ocultar/mostrar elementos de UI según permisos
2. **Dashboard Admin**
   - Métricas en tiempo real: ventas, pedidos activos, reservas, empleados
   - Gráficos de rendimiento diario/semanal
   - Accesos directos a todos los módulos CRUD
   - Registro de actividad reciente (logs de acciones)
3. **Gestión de Usuarios y Permisos**
   - Asignación de roles desde el panel
   - Activación/desactivación de cuentas
   - Restablecimiento de contraseñas administrativas
   - Auditoría básica de accesos

---

## 🔄 FASE 5: INTEGRACIÓN DE FLUJOS DE NEGOCIO
1. **Flujo de Atención al Cliente**
   - Reserva → Asignación de mesa → Creación de pedido → Cocina → Servicio → Pago
   - Actualización de estados en tiempo real con Streams
2. **Control de Inventario**
   - Descuento automático de ingredientes al confirmar pedido
   - Alertas de stock mínimo
   - Relación compras-proveedores con actualización de inventario
3. **Cálculo de Costos**
   - Costo de receta basado en ingredientes
   - Margen de ganancia por platillo
   - Reporte de rentabilidad básico

---

## 📱 FASE 6: UI/UX, RESPONSIVE Y MULTIPLATAFORMA
1. **Adaptación de pantallas**
   - Layouts para móvil (vertical), tablet (grid), desktop (sidebar + contenido)
   - Breakpoints definidos para cada plataforma
2. **Experiencia de usuario**
   - Skeletons de carga
   - Estados vacíos con ilustraciones/iconos
   - Validaciones en tiempo real en formularios
   - Snackbars y diálogos consistentes
3. **Optimización por plataforma**
   - Android: Iconos adaptativos, permisos, navegación nativa
   - Web: SEO básico, favicon, carga optimizada, URL routing
   - Windows: Barra de título personalizada, atajos de teclado, tamaño de ventana

---

##  FASE 7: TESTING, OPTIMIZACIÓN Y DESPLIEGUE
1. **Pruebas**
   - Unitarias para repositorios y utilidades
   - Widget tests para componentes críticos
   - Integración para flujos de autenticación y CRUD
2. **Optimización**
   - Lazy loading en listas largas
   - Paginación o límites en queries de Firestore
   - Compresión de imágenes
   - Eliminación de imports no usados
3. **Despliegue**
   - Android: APK/AAB firmado, configuración de versión
   - Web: Build optimizado, configuración de hosting
   - Windows: Executable, instalador básico
   - Firebase Hosting (para web) y distribución interna

---

## 📦 FASE 8: GESTIÓN DE CÓDIGO Y GITHUB
1. **Estructura de ramas**
   - `main` (producción)
   - `develop` (integración)
   - `feature/*` (desarrollo por módulo)
   - `hotfix/*` (correcciones urgentes)
2. **Commits y documentación**
   - Convención de mensajes (feat, fix, docs, refactor, test)
   - README con instrucciones de instalación, configuración Firebase y ejecución
   - Documentación de API/Firestore y estructura de colecciones
3. **Automatización**
   - Script de envío a GitHub
   - Configuración de GitHub Actions (opcional para CI)
   - Etiquetado de versiones

---

# 💻 `enviargithub.dart`

```dart
// enviargithub.dart
// Script para automatizar el envío del proyecto Flutter a GitHub
// Ejecutar con: dart enviargithub.dart

import 'dart:io';

class GitHelper {
  static Future<int> runGit(List<String> args) async {
    try {
      final result = await Process.run('git', args, runInShell: Platform.isWindows);
      stdout.write(result.stdout);
      stderr.write(result.stderr);
      return result.exitCode;
    } catch (e) {
      stderr.writeln('❌ Error ejecutando Git: $e');
      return -1;
    }
  }

  static Future<bool> isGitInstalled() async {
    final result = await Process.run('git', ['--version'], runInShell: Platform.isWindows);
    return result.exitCode == 0;
  }

  static Future<bool> isGitRepo() async {
    final result = await Process.run('git', ['rev-parse', '--is-inside-work-tree'], runInShell: Platform.isWindows);
    return result.exitCode == 0;
  }

  static Future<bool> hasRemote() async {
    final result = await Process.run('git', ['remote', 'get-url', 'origin'], runInShell: Platform.isWindows);
    return result.exitCode == 0;
  }

  static Future<bool> hasUncommittedChanges() async {
    final result = await Process.run('git', ['status', '--porcelain'], runInShell: Platform.isWindows);
    return (result.stdout as String).trim().isNotEmpty;
  }
}

Future<void> main() async {
  stdout.writeln('\n🚀 Iniciando proceso de envío a GitHub...\n');

  // 1. Verificar Git instalado
  if (!await GitHelper.isGitInstalled()) {
    stderr.writeln('❌ Git no está instalado o no está en el PATH.');
    stderr.writeln('   Descárgalo en: https://git-scm.com/downloads');
    exit(1);
  }
  stdout.writeln('✅ Git detectado correctamente.');

  // 2. Verificar si es repositorio Git
  if (!await GitHelper.isGitRepo()) {
    stdout.writeln('📦 No se detectó un repositorio Git. Inicializando...');
    final initCode = await GitHelper.runGit(['init']);
    if (initCode != 0) {
      stderr.writeln('❌ Falló la inicialización del repositorio.');
      exit(1);
    }
    stdout.writeln('✅ Repositorio inicializado.');
  }

  // 3. Verificar cambios sin confirmar
  if (await GitHelper.hasUncommittedChanges()) {
    stdout.writeln(' Se detectaron cambios sin confirmar. Preparando commit...');
    
    // Pedir mensaje de commit
    stdout.write(' Ingresa el mensaje del commit (o presiona Enter para usar el predeterminado): ');
    final input = stdin.readLineSync()?.trim();
    final commitMsg = input!.isEmpty ? 'feat: sync project to github' : input;

    final addCode = await GitHelper.runGit(['add', '.']);
    if (addCode != 0) {
      stderr.writeln('❌ Falló al agregar archivos al stage.');
      exit(1);
    }

    final commitCode = await GitHelper.runGit(['commit', '-m', commitMsg]);
    if (commitCode != 0) {
      stderr.writeln('❌ Falló al crear el commit.');
      exit(1);
    }
    stdout.writeln('✅ Commit creado exitosamente.');
  } else {
    stdout.writeln('️  No hay cambios pendientes para commitear.');
  }

  // 4. Configurar remote origin si no existe
  if (!await GitHelper.hasRemote()) {
    stdout.writeln('\n🔗 No se detectó un repositorio remoto.');
    stdout.write(' Ingresa la URL del repositorio GitHub (ej: https://github.com/usuario/proyectorestaurante.git): ');
    final repoUrl = stdin.readLineSync()?.trim();
    
    if (repoUrl == null || repoUrl.isEmpty) {
      stderr.writeln('❌ URL no proporcionada. Abortando.');
      exit(1);
    }

    final remoteCode = await GitHelper.runGit(['remote', 'add', 'origin', repoUrl]);
    if (remoteCode != 0) {
      stderr.writeln('❌ Falló al agregar el remote origin.');
      exit(1);
    }
    stdout.writeln('✅ Remote origin configurado.');
  }

  // 5. Obtener rama actual
  final branchResult = await Process.run('git', ['branch', '--show-current'], runInShell: Platform.isWindows);
  final currentBranch = (branchResult.stdout as String).trim();
  stdout.writeln('🌿 Rama actual: $currentBranch');

  // 6. Push a GitHub
  stdout.writeln('\n️  Enviando cambios a GitHub...');
  final pushCode = await GitHelper.runGit(['push', '-u', 'origin', currentBranch]);
  
  if (pushCode == 0) {
    stdout.writeln('\n🎉 ¡Proyecto enviado exitosamente a GitHub!');
    stdout.writeln('🔗 Verifica tu repositorio en la URL configurada.');
  } else {
    stderr.writeln('\n❌ Falló el push. Posibles causas:');
    stderr.writeln('   • Credenciales de GitHub no configuradas');
    stderr.writeln('   • URL del repositorio incorrecta');
    stderr.writeln('   • Conflicto de historial (ejecuta: git pull --rebase origin $currentBranch)');
    exit(1);
  }

  exit(0);
}
```

---

# 📝 INSTRUCCIONES DE USO

1. Guarda el script como `enviargithub.dart` en la raíz de tu proyecto
2. Abre una terminal en la carpeta del proyecto
3. Ejecuta: `dart enviargithub.dart`
4. Sigue las indicaciones en consola (mensaje de commit, URL del repositorio)
5. El script validará Git, inicializará el repo si es necesario, hará commit y push automáticamente

**Requisitos previos:**
- Tener Git instalado y en el PATH
- Tener autenticación con GitHub configurada (SSH key o HTTPS con token/credenciales guardadas)
- Tener un repositorio vacío creado en GitHub

---

# ✅ CHECKLIST FINAL DE IMPLEMENTACIÓN

- [ ] Entorno Flutter y Firebase configurado
- [ ] Arquitectura por características implementada
- [ ] Tema italiano aplicado globalmente
- [ ] Autenticación completa (Email + Google)
- [ ] Control de roles (Admin vs Empleado)
- [ ] 15 CRUDs desarrollados con patrón consistente
- [ ] Panel de admin con métricas y accesos rápidos
- [ ] Flujos de negocio integrados (reserva → pedido → pago → inventario)
- [ ] UI responsive para Android, Web y Windows
- [ ] Testing básico implementado
- [ ] Documentación y README actualizado
- [ ] Proyecto sincronizado en GitHub con `enviargithub.dart`

¿Necesitas que profundice en alguna fase específica, ajuste la priorización de CRUDs, o añada integración con CI/CD y GitHub Actions?
