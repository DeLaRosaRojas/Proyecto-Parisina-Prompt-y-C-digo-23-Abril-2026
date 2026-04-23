¡Hola! Como desarrollador senior, me encanta la estructura que planteas. Es un proyecto sólido y con un flujo de trabajo muy profesional. Vamos a aterrizar este plan de trabajo para el proyecto **parisinacrud**, integrando la potencia de Flutter, Firebase y la lógica de gestión de estados con un toque de "antigravity" (enfoque ágil y ligero).

Aquí tienes el plan de ejecución paso a paso:

---

## 1. Preparación del Entorno y Estructura
Primero, establecemos la base física del proyecto en tu sistema de archivos.

* **Comandos en terminal:**
    ```bash
    mkdir xflutterdelarosa0559
    cd xflutterdelarosa0559
    flutter create crudparisina
    cd crudparisina
    ```

---

## 2. Configuración en Firebase Console
Para que el CRUD funcione, necesitamos el motor de datos:

1.  Ve a [Firebase Console](https://console.firebase.google.com/).
2.  Crea un proyecto llamado **parisinacrud**.
3.  Habilita **Cloud Firestore** en modo de prueba (para facilitar el desarrollo inicial).
4.  Crea una colección llamada `telas`. No necesitas agregar documentos manualmente aún, el código lo hará por ti.
5.  Registra tu app (Android/iOS/Web) y sigue el asistente para descargar el archivo `google-services.json` (para Android) o configurar `FirebaseOptions`.

---

## 3. Integración de Librerías (pubspec.yaml)
Para implementar Firebase y mantener un código limpio (antigravity), usaremos estas dependencias:

### Modificación del archivo `pubspec.yaml`
Debes abrir el archivo y añadir lo siguiente bajo la sección `dependencies`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  # Core de Firebase
  firebase_core: ^2.27.0
  # Base de datos Firestore
  cloud_firestore: ^4.15.8
  # Para un diseño más atractivo
  google_fonts: ^6.2.1
```

**Nota:** Después de guardar, ejecuta `flutter pub get` en tu terminal.

---

## 4. Metodología de Trabajo: Agentes y Roles
Para un desarrollo guiado y profesional, dividiremos el flujo en **Agentes de Software** virtuales:

| Agente | Rol | Skill (Habilidad) |
| :--- | :--- | :--- |
| **Arquitecto** | Definir estructura | Configuración de Firebase y `main.dart` |
| **Data Modeler** | Estructura de datos | Creación del modelo `Tela` |
| **Backend Dev** | Lógica CRUD | Métodos de Firestore |
| **UI Designer** | Interfaz de Usuario | Widgets con colores "Parisina" (Rosa/Blanco/Gris) |

---

## 5. Implementación del Código Funcional

### Estructura de Carpetas Recomendada
```text
lib/
├── models/
│   └── tela_model.dart
├── services/
│   └── firebase_service.dart
├── screens/
│   ├── home_screen.dart
│   └── add_edit_tela.dart
└── main.dart
```

### A. Modelo de Datos (`models/tela_model.dart`)
```dart
class Tela {
  String id;
  String nombre;
  double precio;
  int stock;

  Tela({required this.id, required this.nombre, required this.precio, required this.stock});

  // Convertir de Firestore a Objeto
  factory Tela.fromMap(Map<String, dynamic> data, String id) {
    return Tela(
      id: id,
      nombre: data['nombre'] ?? '',
      precio: (data['precio'] ?? 0).toDouble(),
      stock: data['stock'] ?? 0,
    );
  }

  // Convertir de Objeto a Map para Firestore
  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'precio': precio,
      'stock': stock,
    };
  }
}
```

### B. Servicio CRUD (`services/firebase_service.dart`)
```dart
import 'cloud_firestore/cloud_firestore.dart';
import '../models/tela_model.dart';

class FirebaseService {
  final CollectionReference telasRef = FirebaseFirestore.instance.collection('telas');

  // LEER (Stream para actualización en tiempo real)
  Stream<List<Tela>> getTelas() {
    return telasRef.snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Tela.fromMap(doc.data() as Map<String, dynamic>, doc.id)).toList());
  }

  // CREAR
  Future<void> addTela(Tela tela) => telasRef.add(tela.toMap());

  // ACTUALIZAR
  Future<void> updateTela(Tela tela) => telasRef.doc(tela.id).update(tela.toMap());

  // BORRAR
  Future<void> deleteTela(String id) => telasRef.doc(id).delete();
}
```

### C. Interfaz Principal (`screens/home_screen.dart`)
Utilizaremos un color rosa vibrante (característico de la marca) y tarjetas elegantes.

```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import '../models/tela_model.dart';

class HomeScreen extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Inventario Parisina'),
        backgroundColor: Colors.pinkAccent,
      ),
      body: StreamBuilder<List<Tela>>(
        stream: _service.getTelas(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          
          var telas = snapshot.data!;
          return ListView.builder(
            itemCount: telas.length,
            itemBuilder: (context, index) {
              return Card(
                elevation: 4,
                margin: EdgeInsets.symmetric(horizontal: 10, vertical: 5),
                child: ListTile(
                  leading: CircleAvatar(backgroundColor: Colors.pink[100], child: Icon(Icons.dry_cleaning, color: Colors.pink)),
                  title: Text(telas[index].nombre, style: TextStyle(fontWeight: FontWeight.bold)),
                  subtitle: Text("Precio: \$${telas[index].precio} - Stock: ${telas[index].stock}"),
                  trailing: IconButton(
                    icon: Icon(Icons.delete, color: Colors.redAccent),
                    onPressed: () => _service.deleteTela(telas[index].id),
                  ),
                  onTap: () => _mostrarFormulario(context, telas[index]),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Colors.pinkAccent,
        child: Icon(Icons.add),
        onPressed: () => _mostrarFormulario(context, null),
      ),
    );
  }

  void _mostrarFormulario(BuildContext context, Tela? tela) {
    // Aquí se llamaría a un diálogo o pantalla para ingresar Nombre, Precio y Stock
    // Por brevedad, se omite el código del Dialog pero se asume el uso de TextEditingControllers
  }
}
```

---

## 6. Práctica Guiada para Estudiantes (Enfoque Antigravity)

**¿Qué es Antigravity en esta práctica?**
Es el concepto de eliminar la "pesadez" del código mediante la **reactividad**. 

1.  **Observación:** En lugar de recargar la pantalla manualmente, usamos `StreamBuilder`. El código "flota" porque reacciona solo cuando la base de datos cambia.
2.  **Desafío:** Pide a los alumnos que implementen el botón de "Actualizar" dentro de la tarjeta. Deben notar que, al cambiar el precio en la consola de Firebase, la app se actualiza **instantáneamente** sin tocar el teléfono.
3.  **Modularidad:** Cada archivo tiene una sola responsabilidad. Si falla la base de datos, solo tocas `firebase_service.dart`. Si falla el diseño, solo `home_screen.dart`.

¿Te gustaría que desarrollemos el código completo del formulario de entrada para que los estudiantes solo tengan que copiar y probar?
