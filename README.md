# Agente Inteligente de Gestión de Inventarios

Este proyecto implementa un **agente inteligente** para la gestión de inventarios en una ferretería, utilizando **Lógica de Primer Orden (FOL)** con la librería `pyDatalog` en Python. El agente toma decisiones basadas en reglas de inferencia para optimizar el stock, minimizar costos y maximizar la eficiencia en la reposición de productos.

## Características Principales

- **Reglas de Inferencia**: Identifica productos que necesitan reposición, calcula la cantidad a pedir, detecta productos críticos y prioriza productos con alta demanda.
- **Integración con SQLite**: Almacena los datos de los productos (stock, mínimo requerido, costo, ventas mensuales) en una base de datos SQLite.
- **Decisiones Automatizadas**: Determina cuándo y cuánto pedir de cada producto con base en el stock actual, la demanda y los costos.

## Reglas de Inferencia Implementadas

### 1. Necesita Reposición
Identifica productos que están por debajo del stock mínimo.

```python
necesita_reposicion(X) <= (
    producto(X) &
    stock_actual(X, Y) &
    minimo_stock(X, Z) &
    (Y < Z)
)
```

### 2. Realizar Pedido
Calcula la cantidad que se debe pedir para reponer el stock al nivel mínimo.

```python
realizar_pedido(X, Cantidad) <= (
    necesita_reposicion(X) &
    stock_actual(X, Y) &
    minimo_stock(X, Z) &
    (Cantidad == Z - Y)
)
```

### 3. Productos Críticos
Detecta productos que están por debajo del 20% del stock mínimo y requieren atención inmediata.

```python
producto_critico(X) <= (
    producto(X) &
    stock_actual(X, Y) &
    minimo_stock(X, Z) &
    (Y < 0.2 * Z)
)
```

### 4. Alta Rotación
Identifica productos con alta demanda (más de 100 ventas mensuales).

```python
alta_rotacion(X) <= (
    producto(X) &
    ventas_mensuales(X, V) &
    (V > 100)
)
```

### 5. Bajo Stock y Alta Demanda
Prioriza productos que necesitan reposición y tienen alta demanda.

```python
bajo_stock_alta_demanda(X) <= (
    necesita_reposicion(X) &
    alta_rotacion(X)
)
```

## Estructura del Proyecto

- `inventario.db`: Base de datos SQLite con la información de los productos.
- `main.py`: Script principal con la lógica del agente inteligente.
- `README.md`: Documentación del proyecto.

## Requisitos

### Requisitos de Software
- Python 3.x

### Instalación de Librerías

Ejecuta el siguiente comando para instalar las dependencias:

```bash
pip install pyDatalog
```

## Ejecución

Clona el repositorio y accede al directorio del proyecto:

```bash
git clone https://github.com/luisariass/inteligent-Agent-FOL.git
cd tu-repositorio
```

Ejecuta el script principal:

```bash
python main.py
```

### Ejemplo de Salida en Consola

```plaintext
=== Productos a reponer ===
X               
----------------
Lijas           
Sierra          
Destornilladores
Alicates        
Brochas         
Pintura         
Clavos          
Cemento         
Martillo        

=== Pedidos necesarios ===
X                | Cantidad
-----------------|---------
Lijas            | 100     
Sierra           | 5       
Destornilladores | 5       
Alicates         | 14      
Brochas          | 20      
Pintura          | 5       
Clavos           | 10      
Cemento          | 1       
Martillo         | 10      

=== Productos críticos ===
X       
--------
Alicates

=== Productos con alta rotación ===
X        
---------
Lijas    
Clavos   
Tornillos
Martillo 

=== Productos con bajo stock y alta demanda ===
X       
--------
Lijas   
Clavos  
Martillo      
```

## Personalización

Puedes modificar los datos de los productos en `main.py` para probar diferentes escenarios. Modifica los valores de `stock`, `minimo`, `costo` y `ventas_mensuales` en la sección de inserción de datos.

```python
cursor.executemany('''
    INSERT INTO productos (nombre, stock, minimo, costo, ventas_mensuales)
    VALUES (?, ?, ?, ?, ?)
''', [
    ('Martillo', 20, 30, 5.0, 120),
    ('Cemento', 49, 50, 15.0, 80),
    # Añade más productos aquí
])
```

## Licencia

Este proyecto está bajo la licencia MIT. Consulta el archivo `LICENSE` para más detalles.

---

Desarrollado con ❤️ por **Luis Daniel Arias Marrugo**.
