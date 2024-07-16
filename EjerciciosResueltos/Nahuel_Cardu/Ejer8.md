### 1. Análisis de Requerimientos

#### Ejercicio 1:
Un cliente te solicita una aplicación web para gestionar su inventario. Define los requisitos funcionales y no funcionales del sistema.

**Requisitos Funcionales:**
- **Registro y autenticación:** Permitir a los usuarios registrarse y autenticarse.
- **Gestión de productos:** Añadir, editar, eliminar y visualizar productos en el inventario.
- **Búsqueda y filtrado:** Buscar y filtrar productos por diferentes criterios (nombre, categoría, fecha de ingreso, etc.).
- **Control de stock:** Monitorear la cantidad de productos disponibles en el inventario.
- **Generación de reportes:** Generar reportes sobre el estado del inventario, movimientos de productos, etc.
- **Historial de movimientos:** Registrar y visualizar el historial de movimientos de productos (entradas y salidas).

**Requisitos No Funcionales:**
- **Seguridad:** Protección de datos mediante cifrado y control de acceso.
- **Rendimiento:** La aplicación debe cargar y responder en menos de 3 segundos para cualquier operación común.
- **Escalabilidad:** La aplicación debe ser capaz de manejar un incremento del 100% en el número de productos sin degradación significativa del rendimiento.
- **Usabilidad:** Interfaz intuitiva y fácil de usar, con soporte para múltiples dispositivos (responsive design).
- **Mantenimiento:** Código bien documentado y modular para facilitar el mantenimiento y las actualizaciones.
- **Disponibilidad:** La aplicación debe estar disponible el 99.9% del tiempo.

#### Ejercicio 2:
Redacta un caso de uso para la funcionalidad de "Agregar un nuevo producto" en la aplicación web del ejercicio 1.

**Caso de Uso: Agregar un Nuevo Producto**

**Actor:** Usuario (Admin)

**Descripción:** Este caso de uso permite al usuario agregar un nuevo producto al inventario.

**Precondiciones:**
- El usuario debe estar autenticado y tener permisos de administrador.

**Flujo Principal:**
1. El usuario selecciona la opción "Agregar Producto" desde el menú principal.
2. El sistema muestra el formulario de "Agregar Producto".
3. El usuario completa los campos requeridos (nombre, descripción, categoría, precio, cantidad).
4. El usuario selecciona el botón "Guardar".
5. El sistema valida los datos ingresados.
6. El sistema guarda el nuevo producto en la base de datos.
7. El sistema muestra un mensaje de confirmación al usuario.

**Postcondiciones:**
- El nuevo producto se ha añadido al inventario y es visible en la lista de productos.

**Flujos Alternativos:**
- **A1:** Si el sistema detecta datos inválidos (por ejemplo, campos vacíos), muestra un mensaje de error y solicita al usuario corregir los datos antes de continuar.

### 2. Diseño del Sistema

#### Ejercicio 3:
Elabora un diagrama de flujo de datos para la aplicación web del ejercicio 1.

**Diagrama de Flujo de Datos (Nivel 0):**
```
[Usuario] --(Solicita Agregar Producto)--> [Formulario de Agregar Producto]
[Formulario de Agregar Producto] --(Envía Datos)--> [Validación de Datos]
[Validación de Datos] --(Datos Válidos)--> [Guardar Producto en BD]
[Guardar Producto en BD] --(Confirmación)--> [Usuario]
```

#### Ejercicio 4:
Diseña la interfaz de usuario para la pantalla de "Inicio" de la aplicación web del ejercicio 1.

**Interfaz de Usuario (Pantalla de Inicio):**
- **Barra de Navegación:**
  - Inicio
  - Productos
  - Reportes
  - Perfil
  - Cerrar Sesión
- **Contenido Principal:**
  - **Bienvenida:** "Bienvenido, [Nombre de Usuario]"
  - **Acciones Rápidas:**
    - Agregar Producto
    - Ver Inventario
    - Generar Reporte
  - **Últimos Movimientos:**
    - Listado de los últimos movimientos en el inventario (entradas y salidas)

### 3. Diseño del Programa

#### Ejercicio 5:
Elige una arquitectura adecuada para la aplicación web del ejercicio 1 y justifica tu elección.

**Arquitectura Propuesta: MVC (Model-View-Controller)**
**Justificación:**
- **Separación de preocupaciones:** MVC separa la lógica de negocio, la interfaz de usuario y la gestión de datos, lo que facilita el mantenimiento y la escalabilidad.
- **Modularidad:** Cada componente (modelo, vista, controlador) se puede desarrollar, probar y mantener de manera independiente.
- **Reutilización de código:** Los modelos y controladores pueden reutilizarse en diferentes vistas.
- **Facilidad de prueba:** Permite realizar pruebas unitarias y de integración de manera más efectiva.

#### Ejercicio 6:
Diseña la base de datos para la aplicación web del ejercicio 1.

**Modelo de Base de Datos:**
- **Tabla Productos:**
  - id_producto (PK)
  - nombre
  - descripcion
  - categoria
  - precio
  - cantidad
  - fecha_ingreso

- **Tabla Movimientos:**
  - id_movimiento (PK)
  - id_producto (FK)
  - tipo_movimiento (entrada/salida)
  - cantidad
  - fecha_movimiento

### 4. Diseño

#### Ejercicio 7:
Implementa la funcionalidad de "Agregar un nuevo producto" en la aplicación web del ejercicio 1 utilizando el lenguaje de programación de tu preferencia.

**Implementación (Ejemplo en Python con Flask):**
```python
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///inventario.db'
db = SQLAlchemy(app)

class Producto(db.Model):
    id_producto = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(80), nullable=False)
    descripcion = db.Column(db.String(200), nullable=False)
    categoria = db.Column(db.String(50), nullable=False)
    precio = db.Column(db.Float, nullable=False)
    cantidad = db.Column(db.Integer, nullable=False)
    fecha_ingreso = db.Column(db.DateTime, nullable=False)

@app.route('/agregar_producto', methods=['POST'])
def agregar_producto():
    data = request.get_json()
    nuevo_producto = Producto(
        nombre=data['nombre'],
        descripcion=data['descripcion'],
        categoria=data['categoria'],
        precio=data['precio'],
        cantidad=data['cantidad'],
        fecha_ingreso=data['fecha_ingreso']
    )
    db.session.add(nuevo_producto)
    db.session.commit()
    return jsonify({"mensaje": "Producto agregado exitosamente"}), 201

if __name__ == '__main__':
    db.create_all()
    app.run(debug=True)
```

#### Ejercicio 8:
Implementa la lógica de negocio para la funcionalidad de "Agregar un nuevo producto" en la aplicación web del ejercicio 1.

**Lógica de Negocio:**
```python
def agregar_producto(data):
    # Validar datos
    if not data['nombre'] or not data['descripcion'] or not data['categoria'] or not data['precio'] or not data['cantidad']:
        return {"error": "Todos los campos son obligatorios"}, 400
    
    # Crear nuevo producto
    nuevo_producto = Producto(
        nombre=data['nombre'],
        descripcion=data['descripcion'],
        categoria=data['categoria'],
        precio=data['precio'],
        cantidad=data['cantidad'],
        fecha_ingreso=data['fecha_ingreso']
    )
    
    # Guardar en la base de datos
    db.session.add(nuevo_producto)
    db.session.commit()
    return {"mensaje": "Producto agregado exitosamente"}, 201
```

### 5. Pruebas

#### Ejercicio 9:
Define un conjunto de pruebas unitarias para la funcionalidad de "Agregar un nuevo producto" en la aplicación web del ejercicio 1.

**Pruebas Unitarias (Ejemplo en Python con PyTest):**
```python
import pytest
from app import app, db, Producto

@pytest.fixture
def client():
    app.config['TESTING'] = True
    with app.test_client() as client:
        with app.app_context():
            db.create_all()
        yield client
        with app.app_context():
            db.drop_all()

def test_agregar_producto_exitoso(client):
    response = client.post('/agregar_producto', json={
        'nombre': 'Producto 1',
        'descripcion': 'Descripción del producto 1',
        'categoria': 'Categoría 1',
        'precio': 10.0,
        'cantidad': 100,
        'fecha_ingreso': '2023-07-01'
    })
    assert response.status_code == 201
    assert response.json['mensaje'] == 'Producto agregado exitosamente'

def test_agregar_producto_datos_faltantes(client):
    response = client.post('/agregar_producto', json={
        'nombre': 'Producto 2',
        'descripcion': '',
        'categoria': 'Categoría 2',
        'precio': 20.0,
        'cantidad': 50,
        'fecha_ingreso': '2023-07-01'
    })
    assert response.status_code == 400
    assert response.json['error'] == 'Todos los campos son obligatorios'
```

#### Ejercicio 10:
Ejecuta pruebas de integración para la funcionalidad de "Agregar un nuevo producto" en la aplicación web del ejercicio 1.

**Pruebas de Integración (Ejemplo en Python con PyTest):

**
```python
def test_integration_agregar_producto(client):
    response = client.post('/agregar_producto', json={
        'nombre': 'Producto Integración',
        'descripcion': 'Descripción del producto integración',
        'categoria': 'Categoría Integración',
        'precio': 30.0,
        'cantidad': 200,
        'fecha_ingreso': '2023-07-01'
    })
    assert response.status_code == 201
    assert response.json['mensaje'] == 'Producto agregado exitosamente'
    
    # Verificar que el producto se agregó en la base de datos
    producto = Producto.query.filter_by(nombre='Producto Integración').first()
    assert producto is not None
    assert producto.descripcion == 'Descripción del producto integración'
```

### 6. Despliegue del Programa

#### Ejercicio 11:
Definir un plan de despliegue para la aplicación web del ejercicio 1.

**Plan de Despliegue:**
1. **Preparación del Entorno:**
   - Configurar un servidor con el sistema operativo adecuado.
   - Instalar las dependencias necesarias (Python, Flask, SQLAlchemy, etc.).
2. **Configuración del Servidor:**
   - Configurar un servidor web (Nginx o Apache) para manejar las solicitudes HTTP.
   - Configurar un WSGI server (Gunicorn o uWSGI) para servir la aplicación Flask.
3. **Despliegue del Código:**
   - Clonar el repositorio del código en el servidor.
   - Configurar el entorno virtual y las dependencias del proyecto.
4. **Configuración de la Base de Datos:**
   - Configurar y crear la base de datos.
   - Ejecutar las migraciones necesarias para preparar la base de datos.
5. **Pruebas y Validación:**
   - Ejecutar pruebas para asegurar que la aplicación funcione correctamente en el entorno de producción.
6. **Monitoreo y Mantenimiento:**
   - Configurar herramientas de monitoreo para asegurar la disponibilidad y el rendimiento de la aplicación.
   - Establecer procedimientos de mantenimiento para actualizaciones y correcciones de errores.

#### Ejercicio 12:
Despliega la aplicación web del ejercicio 1 en un servidor de producción.

**Despliegue:**
```bash
# Clonar el repositorio
git clone https://github.com/usuario/inventario-web.git

# Configurar el entorno virtual
cd inventario-web
python3 -m venv venv
source venv/bin/activate

# Instalar dependencias
pip install -r requirements.txt

# Configurar la base de datos
export FLASK_APP=app
flask db upgrade

# Ejecutar la aplicación con Gunicorn
gunicorn -w 4 -b 0.0.0.0:8000 app:app
```

### 7. Mantenimiento

#### Ejercicio 13:
Definir un plan de mantenimiento para la aplicación web del ejercicio 1.

**Plan de Mantenimiento:**
- **Actualizaciones de Seguridad:** Monitorizar y aplicar parches de seguridad regularmente.
- **Monitoreo y Registro:** Utilizar herramientas de monitoreo (como Prometheus) para detectar y solucionar problemas de rendimiento.
- **Copia de Seguridad:** Realizar copias de seguridad periódicas de la base de datos y del código fuente.
- **Documentación:** Mantener la documentación actualizada para facilitar el mantenimiento y la incorporación de nuevos desarrolladores.
- **Pruebas Regulares:** Ejecutar pruebas automatizadas regularmente para detectar y corregir errores.

#### Ejercicio 14:
Implementa una corrección de errores para un problema detectado en la aplicación web del ejercicio 1.

**Corrección de Errores (Ejemplo):**
```python
# Problema detectado: El campo "precio" no permite valores decimales

# Corrección en el modelo de datos
class Producto(db.Model):
    id_producto = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(80), nullable=False)
    descripcion = db.Column(db.String(200), nullable=False)
    categoria = db.Column(db.String(50), nullable=False)
    precio = db.Column(db.Numeric, nullable=False)  # Cambiado de Float a Numeric
    cantidad = db.Column(db.Integer, nullable=False)
    fecha_ingreso = db.Column(db.DateTime, nullable=False)

# Migración de la base de datos para aplicar el cambio
# Crear un nuevo archivo de migración y aplicar la migración
flask db migrate -m "Cambiar tipo de dato de precio a Numeric"
flask db upgrade
```

### 8. Nos preparamos para nuevos retos

#### Ejercicio 15:
Arme un equipo de trabajo y defina los roles para realizar los ejercicios anteriores para un futuro dominio de aplicación relacionado con inteligencia artificial generativa.

**Equipo de Trabajo:**
- **Líder de Proyecto:** Coordina las actividades del equipo y asegura el cumplimiento de los plazos.
- **Analista de Requerimientos:** Define los requisitos funcionales y no funcionales del sistema.
- **Diseñador de UI/UX:** Diseña la interfaz de usuario y asegura una experiencia de usuario óptima.
- **Arquitecto de Software:** Define la arquitectura del sistema y toma decisiones técnicas clave.
- **Desarrollador Backend:** Implementa la lógica del servidor y la integración con la base de datos.
- **Desarrollador Frontend:** Desarrolla la interfaz de usuario y la lógica del cliente.
- **Ingeniero de Pruebas:** Define y ejecuta pruebas unitarias, de integración y de aceptación.
- **Administrador de Sistemas:** Configura y mantiene el entorno de despliegue y los servidores.
- **Especialista en IA:** Desarrolla y entrena modelos de inteligencia artificial generativa.

Cada miembro del equipo colaborará en sus respectivas áreas para garantizar que el proyecto se desarrolle de manera eficiente y efectiva, asegurando que se cumplan los objetivos y se aborden los desafíos técnicos y de negocio.