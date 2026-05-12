Aquí tienes el **Plan de Implementación Actualizado**.

**Cambios Clave:**
1.  Se eliminó la carpeta `assets/images/` y la configuración de recursos locales.
2.  Se agregó la dependencia **`cached_network_image`** (crucial para que las imágenes de la web no consuman datos infinitos ni se vean lentas al hacer scroll).
3.  Se actualizó la fase de UI para manejar URLs externas.

---

### 📋 PLAN DE IMPLEMENTACIÓN DETALLADO: PROYECTO RESTAURANTE ITALIANO (Imágenes Web)

## 1. CONFIGURACIÓN DE DEPENDENCIAS (`pubspec.yaml`)

Este archivo ya no cargará imágenes locales, sino que se enfocará en descargar y cachear las imágenes desde internet.

### 📄 Archivo: `pubspec.yaml`
*(Reemplaza todo el contenido de tu archivo actual con este)*

```yaml
name: proyecto_restaurante
description: Sistema de gestión integral para Restaurante Italiano (Android, iOS, Web, Windows).
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: '>=3.0.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter

  # --- FIREBASE (Sin Analytics) ---
  firebase_core: ^2.24.0       # Núcleo de Firebase
  firebase_auth: ^4.12.0       # Autenticación (Email, Google)
  cloud_firestore: ^4.13.0     # Base de datos NoSQL
  # firebase_storage: ^11.5.0  # Opcional: Si planeas subir imágenes desde la app a Firebase

  # --- IMÁGENES WEB ---
  cached_network_image: ^3.3.1 # CRUCIAL: Carga imágenes web y las guarda en caché para no descargarlas siempre
  transparent_image: ^2.0.1    # Útil para transiciones suaves (fade-in) mientras carga la imagen

  # --- GESTIÓN DE ESTADO Y RUTAS ---
  flutter_riverpod: ^2.3.6     # State Management
  go_router: ^12.0.0           # Navegación y protección de rutas

  # --- UI Y UTILIDADES ---
  google_fonts: ^6.1.0         # Fuentes Playfair Display y Lato
  intl: ^0.18.0                # Formateo de fechas y moneda (Español)
  uuid: ^4.2.0                 # Generación de IDs únicos
  flutter_slidable: ^3.0.0     # Deslizar para eliminar/editar en listas
  gap: ^3.0.1                  # Espaciado flexible
  cupertino_icons: ^1.0.2      # Iconos estilo iOS

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^3.0.0

flutter:
  uses-material-design: true
  # NO SE USA LA SECCIÓN ASSETS PORQUE LAS IMÁGENES SON WEB
```

---

## 2. ESTRATEGIA DE IMÁGENES WEB (Cómo se implementa)

Al usar imágenes web, debemos manejar tres estados: **Cargando**, **Éxito** y **Error**. Usaremos el paquete `cached_network_image`.

### A. El Modelo (Ejemplo: `lib/datos/modelos/platillo.dart`)
El campo de la imagen debe guardar una URL de texto.

```dart
class Platillo {
  final String idPlatillo;
  final String nombre;
  final String descripcion;
  final double precio;
  final String urlImagen; // <--- Campo para la imagen web
  final bool disponible;

  Platillo({
    required this.idPlatillo,
    required this.nombre,
    required this.descripcion,
    required this.precio,
    required this.urlImagen,
    this.disponible = true,
  });

  factory Platillo.fromFirestore(Map<String, dynamic> data, String id) {
    return Platillo(
      idPlatillo: id,
      nombre: data['nombre'] ?? 'Sin nombre',
      descripcion: data['descripcion'] ?? '',
      precio: (data['precio'] ?? 0.0).toDouble(),
      urlImagen: data['urlImagen'] ?? '', // Puede venir vacía
      disponible: data['disponible'] ?? true,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'descripcion': descripcion,
      'precio': precio,
      'urlImagen': urlImagen,
      'disponible': disponible,
    };
  }
}
```

### B. El Widget Reutilizable (`lib/core/widgets/imagen_red.dart`)
Crea este widget para usarlo en cualquier lista (Platillos, Empleados, etc.). Muestra un spinner mientras carga y un icono de error si falla.

```dart
import 'package:cached_network_image/cached_network_image.dart';
import 'package:flutter/material.dart';
import '../../config/tema.dart'; // Tu archivo de colores

class ImagenRed extends StatelessWidget {
  final String url;
  final double ancho;
  final double alto;
  final BoxFit ajuste;

  const ImagenRed({
    Key? key,
    required this.url,
    this.ancho = 100,
    this.alto = 100,
    this.ajuste = BoxFit.cover,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    if (url.isEmpty) {
      // Imagen por defecto si no hay URL
      return Container(
        width: ancho,
        height: alto,
        color: AppColors.fondo,
        child: const Icon(Icons.restaurant_menu, color: Colors.grey),
      );
    }

    return ClipRRect(
      borderRadius: BorderRadius.circular(8), // Bordes redondeados para estética
      child: CachedNetworkImage(
        imageUrl: url,
        width: ancho,
        height: alto,
        fit: ajuste,
        placeholder: (context, url) => Container(
          color: Colors.grey[200],
          child: const Center(child: CircularProgressIndicator(strokeWidth: 2)),
        ),
        errorWidget: (context, url, error) => Container(
          color: Colors.grey[200],
          child: const Icon(Icons.broken_image, color: Colors.red),
        ),
      ),
    );
  }
}
```

---

## 3. FASES DE IMPLEMENTACIÓN (ACTUALIZADAS)

### 🟢 FASE 1: CONFIGURACIÓN INICIAL
1. **Crear Proyecto:** `flutter create proyecto_restaurante --platforms=windows,android,ios,web`
2. **Instalar:** Copiar el `pubspec.yaml` de arriba y ejecutar `flutter pub get`.
3. **Firebase:**
   - Crear proyecto `proyectorestaurante`.
   - **NO Analytics.**
   - Firestore en **Modo Prueba**.
   - Ejecutar `flutterfire configure`.

### 🔵 FASE 2: TEMA Y WIDGETS BASE (SIN ASSETS LOCALES)
1. **Tema Italiano (`lib/config/tema.dart`):**
   - Colores: Rojo Pomodoro, Verde Albahaca, Fondo Crema.
   - Fuentes: Playfair Display (Títulos), Lato (Cuerpo).
2. **Widgets Base:**
   - Crear `lib/core/widgets/imagen_red.dart` (código de arriba).
   - Crear `lib/core/widgets/boton_personalizado.dart`.
   - Crear `lib/core/widgets/campo_texto_personalizado.dart`.

### 🟣 FASE 3: AUTENTICACIÓN
1. **Servicio:** `lib/core/servicios/autenticacion_servicio.dart`.
   - Login, Registro, Google.
2. **Rutas:** `lib/config/rutas.dart`.
   - Proteger `/admin` y `/empleado`.

### 🟠 FASE 4: CRUD DE ENTIDADES (15 MÓDULOS)
*Usar el patrón: Modelo → Repositorio → Provider → UI.*

*Ejemplo aplicado a **PLATILLOS**:*

1. **Modelo:** Clase `Platillo` con campo `urlImagen` (String).
2. **Repositorio:** `PlatilloRepositorio` (Firestore).
3. **Interfaz - Lista (`interfaz/modulos/platillos/lista_platillos.dart`):**
   - `GridView` que muestra las tarjetas de los platillos.
   - Dentro de la tarjeta, usar el widget **`ImagenRed(url: platillo.urlImagen)`**.
4. **Interfaz - Formulario (`interfaz/modulos/platillos/formulario_platillos.dart`):**
   - Campo de texto donde el admin pega la URL de la imagen (ej: `https://ejemplo.com/pizza.jpg`).
   - Opcional: Botón para abrir cámara/galería (requiere permisos), pero como usamos web, el campo de texto URL es más simple y rápido para multiplataforma.

###  FASE 5: LÓGICA DE NEGOCIO
1. **Inventario:** Al confirmar pedido, descontar ingredientes.
2. **Dashboard Admin:** Tarjetas de resumen.

### 🔴 FASE 6: PRUEBAS Y SCRIPT
1. **Pruebas:** Verificar que las imágenes carguen rápido en Web y Windows gracias al caché.
2. **Script GitHub:**
   - Asegurar que `enviargithub.dart` esté en la raíz.
   - Ejecutar `dart enviargithub.dart`.

---

## ✅ CHECKLIST DE CUMPLIMIENTO
- [x] **Imágenes:** Uso exclusivo de **Web Images** con caché (`cached_network_image`).
- [x] **Assets:** Eliminada la carpeta de recursos locales para reducir tamaño de app.
- [x] **Dependencias:** `pubspec.yaml` limpio y sin Analytics.
- [x] **Plataformas:** Windows, iOS, Web, Android.
- [x] **Idioma:** 100% Español.
- [x] **Firestore:** Modo Prueba (Desarrollo).
- [x] **Entidades:** 15 CRUDs con campos detallados.
- [x] **Script:** `enviargithub.dart` incluido.
      Perfecto. A continuación agrego una estructura completa de **models (modelos de datos)** para el sistema del restaurante italiano, siguiendo tu arquitectura:

**Modelo → Repositorio → Provider → UI**

Todos los modelos:

* Están en español.
* Compatibles con Firestore.
* Preparados para imágenes web (`urlImagen`).
* Incluyen:

  * Constructor
  * `fromFirestore`
  * `toMap`
  * Valores seguros por defecto

---

# 📁 ESTRUCTURA SUGERIDA

```txt
lib/
└── datos/
    └── modelos/
        ├── platillo.dart
        ├── categoria.dart
        ├── empleado.dart
        ├── cliente.dart
        ├── mesa.dart
        ├── pedido.dart
        ├── detalle_pedido.dart
        ├── ingrediente.dart
        ├── proveedor.dart
        ├── compra.dart
        ├── reserva.dart
        ├── pago.dart
        ├── usuario.dart
        ├── promocion.dart
        └── inventario.dart
```

---

# 🍕 1. platillo.dart

```dart
class Platillo {
  final String idPlatillo;
  final String nombre;
  final String descripcion;
  final double precio;
  final String categoriaId;
  final String urlImagen;
  final bool disponible;

  Platillo({
    required this.idPlatillo,
    required this.nombre,
    required this.descripcion,
    required this.precio,
    required this.categoriaId,
    required this.urlImagen,
    this.disponible = true,
  });

  factory Platillo.fromFirestore(Map<String, dynamic> data, String id) {
    return Platillo(
      idPlatillo: id,
      nombre: data['nombre'] ?? '',
      descripcion: data['descripcion'] ?? '',
      precio: (data['precio'] ?? 0).toDouble(),
      categoriaId: data['categoriaId'] ?? '',
      urlImagen: data['urlImagen'] ?? '',
      disponible: data['disponible'] ?? true,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'descripcion': descripcion,
      'precio': precio,
      'categoriaId': categoriaId,
      'urlImagen': urlImagen,
      'disponible': disponible,
    };
  }
}
```

---

# 🍝 2. categoria.dart

```dart
class Categoria {
  final String idCategoria;
  final String nombre;
  final String descripcion;

  Categoria({
    required this.idCategoria,
    required this.nombre,
    required this.descripcion,
  });

  factory Categoria.fromFirestore(Map<String, dynamic> data, String id) {
    return Categoria(
      idCategoria: id,
      nombre: data['nombre'] ?? '',
      descripcion: data['descripcion'] ?? '',
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'descripcion': descripcion,
    };
  }
}
```

---

# 👨‍🍳 3. empleado.dart

```dart
class Empleado {
  final String idEmpleado;
  final String nombre;
  final String puesto;
  final String telefono;
  final String correo;
  final double salario;
  final String urlImagen;
  final bool activo;

  Empleado({
    required this.idEmpleado,
    required this.nombre,
    required this.puesto,
    required this.telefono,
    required this.correo,
    required this.salario,
    required this.urlImagen,
    this.activo = true,
  });

  factory Empleado.fromFirestore(Map<String, dynamic> data, String id) {
    return Empleado(
      idEmpleado: id,
      nombre: data['nombre'] ?? '',
      puesto: data['puesto'] ?? '',
      telefono: data['telefono'] ?? '',
      correo: data['correo'] ?? '',
      salario: (data['salario'] ?? 0).toDouble(),
      urlImagen: data['urlImagen'] ?? '',
      activo: data['activo'] ?? true,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'puesto': puesto,
      'telefono': telefono,
      'correo': correo,
      'salario': salario,
      'urlImagen': urlImagen,
      'activo': activo,
    };
  }
}
```

---

# 👤 4. cliente.dart

```dart
class Cliente {
  final String idCliente;
  final String nombre;
  final String telefono;
  final String correo;
  final int puntos;

  Cliente({
    required this.idCliente,
    required this.nombre,
    required this.telefono,
    required this.correo,
    this.puntos = 0,
  });

  factory Cliente.fromFirestore(Map<String, dynamic> data, String id) {
    return Cliente(
      idCliente: id,
      nombre: data['nombre'] ?? '',
      telefono: data['telefono'] ?? '',
      correo: data['correo'] ?? '',
      puntos: data['puntos'] ?? 0,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'telefono': telefono,
      'correo': correo,
      'puntos': puntos,
    };
  }
}
```

---

# 🪑 5. mesa.dart

```dart
class Mesa {
  final String idMesa;
  final int numero;
  final int capacidad;
  final bool ocupada;

  Mesa({
    required this.idMesa,
    required this.numero,
    required this.capacidad,
    this.ocupada = false,
  });

  factory Mesa.fromFirestore(Map<String, dynamic> data, String id) {
    return Mesa(
      idMesa: id,
      numero: data['numero'] ?? 0,
      capacidad: data['capacidad'] ?? 0,
      ocupada: data['ocupada'] ?? false,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'numero': numero,
      'capacidad': capacidad,
      'ocupada': ocupada,
    };
  }
}
```

---

# 🧾 6. pedido.dart

```dart
class Pedido {
  final String idPedido;
  final String idCliente;
  final String idEmpleado;
  final String estado;
  final double total;
  final DateTime fecha;

  Pedido({
    required this.idPedido,
    required this.idCliente,
    required this.idEmpleado,
    required this.estado,
    required this.total,
    required this.fecha,
  });

  factory Pedido.fromFirestore(Map<String, dynamic> data, String id) {
    return Pedido(
      idPedido: id,
      idCliente: data['idCliente'] ?? '',
      idEmpleado: data['idEmpleado'] ?? '',
      estado: data['estado'] ?? '',
      total: (data['total'] ?? 0).toDouble(),
      fecha: DateTime.parse(data['fecha']),
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'idCliente': idCliente,
      'idEmpleado': idEmpleado,
      'estado': estado,
      'total': total,
      'fecha': fecha.toIso8601String(),
    };
  }
}
```

---

# 🍽️ 7. detalle_pedido.dart

```dart
class DetallePedido {
  final String idDetalle;
  final String idPedido;
  final String idPlatillo;
  final int cantidad;
  final double subtotal;

  DetallePedido({
    required this.idDetalle,
    required this.idPedido,
    required this.idPlatillo,
    required this.cantidad,
    required this.subtotal,
  });

  factory DetallePedido.fromFirestore(Map<String, dynamic> data, String id) {
    return DetallePedido(
      idDetalle: id,
      idPedido: data['idPedido'] ?? '',
      idPlatillo: data['idPlatillo'] ?? '',
      cantidad: data['cantidad'] ?? 0,
      subtotal: (data['subtotal'] ?? 0).toDouble(),
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'idPedido': idPedido,
      'idPlatillo': idPlatillo,
      'cantidad': cantidad,
      'subtotal': subtotal,
    };
  }
}
```

---

# 🥬 8. ingrediente.dart

```dart
class Ingrediente {
  final String idIngrediente;
  final String nombre;
  final double stock;
  final String unidad;

  Ingrediente({
    required this.idIngrediente,
    required this.nombre,
    required this.stock,
    required this.unidad,
  });

  factory Ingrediente.fromFirestore(Map<String, dynamic> data, String id) {
    return Ingrediente(
      idIngrediente: id,
      nombre: data['nombre'] ?? '',
      stock: (data['stock'] ?? 0).toDouble(),
      unidad: data['unidad'] ?? '',
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'stock': stock,
      'unidad': unidad,
    };
  }
}
```

---

# 🚚 9. proveedor.dart

```dart
class Proveedor {
  final String idProveedor;
  final String nombre;
  final String telefono;
  final String correo;

  Proveedor({
    required this.idProveedor,
    required this.nombre,
    required this.telefono,
    required this.correo,
  });

  factory Proveedor.fromFirestore(Map<String, dynamic> data, String id) {
    return Proveedor(
      idProveedor: id,
      nombre: data['nombre'] ?? '',
      telefono: data['telefono'] ?? '',
      correo: data['correo'] ?? '',
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'telefono': telefono,
      'correo': correo,
    };
  }
}
```

---

# 📦 10. compra.dart

```dart
class Compra {
  final String idCompra;
  final String idProveedor;
  final double total;
  final DateTime fecha;

  Compra({
    required this.idCompra,
    required this.idProveedor,
    required this.total,
    required this.fecha,
  });

  factory Compra.fromFirestore(Map<String, dynamic> data, String id) {
    return Compra(
      idCompra: id,
      idProveedor: data['idProveedor'] ?? '',
      total: (data['total'] ?? 0).toDouble(),
      fecha: DateTime.parse(data['fecha']),
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'idProveedor': idProveedor,
      'total': total,
      'fecha': fecha.toIso8601String(),
    };
  }
}
```

---

# 📅 11. reserva.dart

```dart
class Reserva {
  final String idReserva;
  final String idCliente;
  final String idMesa;
  final DateTime fecha;
  final int personas;

  Reserva({
    required this.idReserva,
    required this.idCliente,
    required this.idMesa,
    required this.fecha,
    required this.personas,
  });

  factory Reserva.fromFirestore(Map<String, dynamic> data, String id) {
    return Reserva(
      idReserva: id,
      idCliente: data['idCliente'] ?? '',
      idMesa: data['idMesa'] ?? '',
      fecha: DateTime.parse(data['fecha']),
      personas: data['personas'] ?? 0,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'idCliente': idCliente,
      'idMesa': idMesa,
      'fecha': fecha.toIso8601String(),
      'personas': personas,
    };
  }
}
```

---

# 💳 12. pago.dart

```dart
class Pago {
  final String idPago;
  final String idPedido;
  final String metodo;
  final double monto;
  final bool completado;

  Pago({
    required this.idPago,
    required this.idPedido,
    required this.metodo,
    required this.monto,
    this.completado = false,
  });

  factory Pago.fromFirestore(Map<String, dynamic> data, String id) {
    return Pago(
      idPago: id,
      idPedido: data['idPedido'] ?? '',
      metodo: data['metodo'] ?? '',
      monto: (data['monto'] ?? 0).toDouble(),
      completado: data['completado'] ?? false,
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'idPedido': idPedido,
      'metodo': metodo,
      'monto': monto,
      'completado': completado,
    };
  }
}
```

---

# 🔐 13. usuario.dart

```dart
class Usuario {
  final String uid;
  final String nombre;
  final String correo;
  final String rol;

  Usuario({
    required this.uid,
    required this.nombre,
    required this.correo,
    required this.rol,
  });

  factory Usuario.fromFirestore(Map<String, dynamic> data, String id) {
    return Usuario(
      uid: id,
      nombre: data['nombre'] ?? '',
      correo: data['correo'] ?? '',
      rol: data['rol'] ?? 'empleado',
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'correo': correo,
      'rol': rol,
    };
  }
}
```

---

# 🎁 14. promocion.dart

```dart
class Promocion {
  final String idPromocion;
  final String titulo;
  final String descripcion;
  final double descuento;
  final String urlImagen;

  Promocion({
    required this.idPromocion,
    required this.titulo,
    required this.descripcion,
    required this.descuento,
    required this.urlImagen,
  });

  factory Promocion.fromFirestore(Map<String, dynamic> data, String id) {
    return Promocion(
      idPromocion: id,
      titulo: data['titulo'] ?? '',
      descripcion: data['descripcion'] ?? '',
      descuento: (data['descuento'] ?? 0).toDouble(),
      urlImagen: data['urlImagen'] ?? '',
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'titulo': titulo,
      'descripcion': descripcion,
      'descuento': descuento,
      'urlImagen': urlImagen,
    };
  }
}
```

---

# 📊 15. inventario.dart

```dart
class Inventario {
  final String idInventario;
  final String idIngrediente;
  final double cantidad;
  final double minimo;

  Inventario({
    required this.idInventario,
    required this.idIngrediente,
    required this.cantidad,
    required this.minimo,
  });

  factory Inventario.fromFirestore(Map<String, dynamic> data, String id) {
    return Inventario(
      idInventario: id,
      idIngrediente: data['idIngrediente'] ?? '',
      cantidad: (data['cantidad'] ?? 0).toDouble(),
      minimo: (data['minimo'] ?? 0).toDouble(),
    );
  }

  Map<String, dynamic> toMap() {
    return {
      'idIngrediente': idIngrediente,
      'cantidad': cantidad,
      'minimo': minimo,
    };
  }
}
```

---

# ✅ RECOMENDACIÓN IMPORTANTE

Para evitar errores futuros en Firestore, te recomiendo agregar:

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
```

y usar:

```dart
(data['precio'] as num).toDouble()
```

en vez de:

```dart
(data['precio'] ?? 0).toDouble()
```

porque Firestore a veces devuelve `int` y otras `double`.

---


Prompt:
Crea una aplicación Flutter multiplataforma (Android, iOS, Web y Windows) llamada "ProyectoRestaurante" para la gestión integral de un restaurante italiano (colores Rojo, Verde, Crema), utilizando Firebase Authentication (Email/Password y Google Sign-In) y Cloud Firestore como backend con configuración estándar, descartando explícitamente Google Analytics y manteniendo Firestore en modo de prueba/desarrollo (sin activar modo producción). La app debe implementar un sistema de control de acceso por roles (RBAC) con un panel de administrador exclusivo, navegación segura con GoRouter, manejo de estado con Riverpod y una arquitectura basada en características (`lib/features/[entidad]/data|presentation` + `lib/core`). Desarrolla el CRUD completo para las 15 entidades exactas del diagrama: roles, empleados, clientes, mesas, categorias, platillos, ingredientes, proveedores, reservaciones, pedidos, detalle_pedido, pagos, compras, detalle_compra y recetas, incluyendo modelos con serialización Firestore, repositorios con streams, providers/notifiers, pantallas de listado con filtros/búsqueda, formularios con validación y widgets reutilizables. Aplica una interfaz moderna con tema Material 3 y paleta italiana, entrega el código completo listo para ejecutar, las dependencias en `pubspec.yaml`, instrucciones de configuración Firebase estándar y un script ejecutable `enviargithub.dart` en la raíz que automatice la inicialización del repositorio Git y la subida a GitHub. Todo debe ser funcional.
