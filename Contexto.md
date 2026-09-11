# Contexto del Proyecto — Semana 4: "Relación de Modelos en Django"

## Integrantes del equipo

| Nombre | Rol |
|--------|-----|
| | Integrante 1 |
| | Integrante 2 |
| | Integrante 3 |

## Objetivo

Implementar un sistema de biblioteca con relaciones entre modelos:
ForeignKey, OneToOneField, ManyToManyField y modelo intermedio con `through`.

## Contexto del laboratorio

El proyecto es **acumulativo**: se parte del resultado de la semana anterior.
Cada semana se trabaja en equipo y se sube a un solo repositorio de GitHub.

## Criterios de evaluación (20 puntos)

| # | Criterio | Puntos |
|---|----------|--------|
| 1 | Configura las relaciones con ForeignKey, OneToOneField y ManyToManyField | 5 |
| 2 | Declara el modelo intermedio con through para la relación con datos propios | 5 |
| 3 | Consulta en ambos sentidos y comprueba el efecto de on_delete | 5 |
| 4 | Entrega el repositorio con el modelo relacional y sus observaciones | 5 |

## Requerimientos específicos

### Modelo Author (ForeignKey)
- Declarar modelo Author con campos propios
- Enlazar Book con Author mediante ForeignKey
- Elegir el on_delete correcto y related_name legible

### Modelo AuthorProfile (OneToOneField)
- Añadir perfil de autor con OneToOneField
- Separar datos biográficos del registro principal

### Modelos Book, Category y Publisher
- Declarar modelos con campos propios y Meta
- Relacionar Book con Category mediante ManyToManyField

### Modelo Publication (through)
- Relacionar Book con Publisher a través del modelo intermedio Publication
- Guardar fecha y edición en el modelo intermedio
- Consultar desde ambos lados

### Migraciones
- Generar y aplicar migraciones
- Comprobar en la base de datos qué tablas se crearon

### Datos de prueba
- Cargar desde el administrador:
  - 2 autores
  - 4 libros (al menos uno en 2 categorías)
  - 3 categorías
  - 2 editoriales

### Consultas en Django Shell
- Consulta de ida: libro.autor
- Consulta de vuelta: autor.libros.all()
- Filtrado con doble guion bajo

### Verificación on_delete
- Provocar borrado de autor con libros (documentar resultado)
- Cambiar on_delete a PROTECT y comparar

### Vista de detalle
- Plantilla de detalle de libro con:
  - Categorías
  - Editorial
  - Datos del autor

### Evidencias
- Diagrama de modelos
- Capturas de consultas
- Capturas del administrador
- Estructura del proyecto en VS Code

## Normas

- Código Python según PEP 8
- Estructura Django: una aplicación por responsabilidad
- Modelos en singular, migraciones versionadas
- settings.py sin credenciales escritas a mano
- Código, nombres de variables y comentarios en **inglés**
- Entregables y explicaciones en **español**

## Recursos

- Python 3.12+
- Node.js 20+
- Git
- Visual Studio Code
- Pillow (para campos de imagen)
