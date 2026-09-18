# Laboratorio 4 — Relación de Modelos en Django

Proyecto Django del Laboratorio 4 (Semana 4) que implementa un sistema de biblioteca para demostrar los distintos tipos de relaciones entre modelos: `ForeignKey`, `OneToOneField`, `ManyToManyField` y un modelo intermedio con `through`.

## 1. Descripción

El objetivo del laboratorio es construir un proyecto Django que modele una biblioteca usando relaciones de modelos reales:

- `Author 1:N Book` mediante `ForeignKey`.
- `Author 1:1 AuthorProfile` mediante `OneToOneField`.
- `Book N:M Category` mediante `ManyToManyField`.
- `Book N:M Publisher` mediante un modelo intermedio `Publication` (con `through`) que almacena datos propios de la relación (fecha de publicación y edición).

Además se consultan las relaciones en ambos sentidos, se comprueba el comportamiento de `on_delete` (`CASCADE` y `PROTECT`) y se documenta toda la evidencia (consultas ORM, resultados de `on_delete`, diagrama y tests).

## 2. Tecnologías

- Python 3.13
- Django 6.1.1
- Pillow 12.3.0 (campos de imagen `ImageField`)
- SQLite (base de datos por defecto)
- Tailwind CSS (estilos en las plantillas)

## 3. Estructura del proyecto

```text
semana4-DAE/
├── manage.py                    # Utilidad de administración de Django
├── requirements.txt             # Dependencias del proyecto
├── config/                      # Proyecto Django (settings, urls, wsgi, asgi)
│   ├── settings.py
│   └── urls.py
├── library/                     # Aplicación principal
│   ├── models.py                # Modelos de datos
│   ├── admin.py                 # Registro en el administrador
│   ├── views.py                 # Vistas lista_libros y detalle_libro
│   ├── urls.py                  # Rutas de la aplicación
│   ├── tests.py                 # Tests de modelos, relaciones y vistas
│   ├── fixtures/initial_data.json  # Datos de prueba
│   ├── migrations/              # Migraciones versionadas
│   └── templates/library/       # Plantillas (base, book_list, book_detail)
├── docs/
│   └── diagrama-modelos.md      # Diagrama ER y observaciones del modelo
├── ORM_QUERIES.txt              # Resultados de las consultas ORM
└── ON_DELETE_RESULTS.txt        # Resultados del comportamiento on_delete
```

## 4. Modelos

- **Author**: autor de libros. Campos: `nombre`, `email`, `bio`, `fecha_nacimiento`.
- **AuthorProfile**: perfil biográfico del autor, vinculado 1:1. Campos: `author` (OneToOne), `direccion`, `telefono`, `foto`.
- **Book**: libro. Campos: `titulo`, `isbn`, `autor` (FK), `categorias` (M2M), `editorial` (M2M vía `Publication`), `fecha_publicacion`, `sinopsis`, `portada`.
- **Category**: categoría de clasificación. Campos: `nombre`, `descripcion`.
- **Publisher**: editorial. Campos: `nombre`, `direccion`, `ciudad`, `sitio_web`.
- **Publication**: modelo intermedio entre `Book` y `Publisher`. Campos: `libro` (FK), `editorial` (FK), `fecha_publicacion`, `edicion`.

## 5. Relaciones

- **ForeignKey** — `Book.autor → Author`: una editorial un autor tiene muchos libros y cada libro pertenece a un autor (1:N).
- **OneToOneField** — `AuthorProfile.author → Author`: cada autor tiene un único perfil (1:1).
- **ManyToManyField** — `Book.categorias → Category`: un libro tiene muchas categorías y una categoría muchos libros (N:M), con tabla intermedia automática.
- **through / Publication** — `Book.editorial → Publisher`: relación N:M con datos propios almacenados en el modelo intermedio `Publication` (`fecha_publicacion` y `edicion`), declarada con `through='Publication'` y `through_fields=('libro', 'editorial')`.

## 6. Diagrama

Consulta el diagrama ER y las observaciones del modelo en [docs/diagrama-modelos.md](docs/diagrama-modelos.md).

## Diagrama de modelos

El siguiente diagrama representa las relaciones entre los modelos implementados:

![Diagrama de modelos](docs/modelo-relacional.png)

Documentación y fuente Mermaid editable en [docs/modelo-relacional.md](docs/modelo-relacional.md).

## 7. Consultas ORM

Los resultados de las consultas ejecutadas en la shell de Django (ida, vuelta y filtros con doble guion bajo) están documentados en [ORM_QUERIES.txt](ORM_QUERIES.txt).

Ejemplo de consultas:

```python
# Consulta de ida: Book -> Author
book.autor

# Consulta de vuelta: Author -> Books
author.books.all()

# Filtrado con double underscore
Book.objects.filter(categorias__nombre="Magical Realism")
```

## 8. on_delete

El comportamiento de `CASCADE` y `PROTECT` verificado en la shell está documentado en [ON_DELETE_RESULTS.txt](ON_DELETE_RESULTS.txt).

- **CASCADE**: al borrar un autor se borran sus libros y su perfil.
- **PROTECT**: al intentar borrar una editorial con publicaciones se lanza `ProtectedError`.

## 9. Tests

Para ejecutar todos los tests:

```bash
python manage.py test
```

Los tests cubren: creación de modelos, relaciones bidireccionales (Book→Author, Author→Books, Book→Categories, Book→Publication→Publisher), filtros con `__`, comportamiento de `CASCADE`/`PROTECT` y las vistas de lista y detalle.

## 10. Instalación

```bash
# 1. Crear y activar un entorno virtual
python -m venv venv
# Windows
venv\Scripts\activate
# Linux/macOS
source venv/bin/activate

# 2. Instalar dependencias
pip install -r requirements.txt
```

## 11. Ejecución

```bash
python manage.py runserver
```

Accede a `http://127.0.0.1:8000/library/` para ver la lista de libros y a `http://127.0.0.1:8000/admin/` para el administrador.

## 12. Migraciones

```bash
# Crear migraciones a partir de los modelos
python manage.py makemigrations

# Aplicar las migraciones a la base de datos
python manage.py migrate

# Verificar que no falten migraciones
python manage.py makemigrations --check
```

## 13. Datos de prueba

Los datos de prueba están en `library/fixtures/initial_data.json` y se cargan con:

```bash
python manage.py loaddata initial_data
```

Contiene: **2 autores**, **3 categorías**, **2 editoriales**, **4 libros** y **5 publicaciones**.

## 14. Observaciones

- `Book.autor` usa `ForeignKey` (1:N) porque un autor tiene muchos libros.
- `AuthorProfile.author` usa `OneToOneField` (1:1) para separar datos biográficos del autor.
- `Book.categorias` usa `ManyToManyField` (N:M) porque un libro pertenece a varias categorías.
- Se usa el modelo intermedio `Publication` (con `through`) para guardar la fecha de publicación y la edición de la relación libro–editorial.
- `on_delete=models.CASCADE` en `Book.autor`, `AuthorProfile.author` y `Publication.libro`; `on_delete=models.PROTECT` en `Publication.editorial`.
- `settings.py` no contiene credenciales escritas: `SECRET_KEY` se lee de la variable de entorno `DJANGO_SECRET_KEY` (con valor de respaldo solo para desarrollo local).