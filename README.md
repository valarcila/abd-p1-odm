# Práctica 1 · ODM · EnVivo (fase 1)

Punto de partida de la práctica 1 de **Ampliación de Bases de Datos** (u-tad).
Aquí está el esqueleto del ODM, con los `#TODO` que hay que completar, y las
pruebas con las que puedes comprobar tu implementación mientras la escribes.

El enunciado completo, con los requisitos y la rúbrica, está en el campus
virtual. Esto es solo el código.

## Cómo empezar

1. **Haz un fork** de este repositorio con el botón *Fork* de GitHub. Uno por
   grupo: trabajad los dos sobre el mismo fork.
2. Clónalo en tu máquina:

   ```bash
   git clone https://github.com/<tu-usuario>/abd-p1-odm.git
   cd abd-p1-odm
   ```

3. Levanta MongoDB, desde la carpeta donde tengas el `compose.yaml` de la
   asignatura:

   ```bash
   docker compose up -d mongo
   ```

4. Instala las dependencias y comprueba que todo arranca:

   ```bash
   uv sync
   uv run python -m pytest ODM_test.py -q
   ```

Al principio fallan todas: eso es lo normal, es lo que hay que implementar.

## Qué hay aquí

| Fichero | Qué es |
|---|---|
| `ODM.py` | El código de partida. **Es lo que hay que completar** |
| `models.yml` | Ejemplo con **un solo modelo**. Hay que sustituirlo por los cuatro de EnVivo |
| `pyproject.toml` | Las dependencias. No hace falta tocarlo |
| `ODM_test.py` | Las pruebas |
| `models_test.yml` | Los modelos que usan las pruebas. **No lo edites** |

## Qué se ve al ejecutar las pruebas

Cuando todo está bien, un punto por prueba:

```
............                                             [100%]
12 passed in 0.3s
```

Mientras no lo esté, cada `F` es una prueba que falla, y abajo sale el nombre
de cada una. El nombre dice qué parte falta, así que se leen de arriba abajo:

```
FAILED ODM_test.py::test_single_document_save - AssertionError: assert None is not None
1 failed, 11 passed in 0.3s
```

Para trabajar sobre una sola:

```bash
uv run python -m pytest ODM_test.py -k test_single_document_save -v
uv run python -m pytest ODM_test.py -x --tb=short   # para en el primer fallo
```

## Qué exige cada prueba

| Prueba | Qué tiene que funcionar |
|---|---|
| `test_model_class_creation` | `initApp` crea una clase por modelo del YAML y la deja en `scope` |
| `test_model_attribute_registration` | La clase conoce sus atributos requeridos, los admitidos y el campo de dirección |
| `test_single_document_save`, `test_multiple_document_save` | `save` inserta un documento nuevo |
| `test_document_deletion` | `delete` lo borra |
| `test_add_attributes_before_save` | `__setattr__` acepta un atributo admitido antes de guardar |
| `test_find_one_model_instance` | `find` devuelve un `ModelCursor` que entrega **objetos modelo**, no diccionarios |
| `test_find_specific_model_instance`, `test_find_multiple_model_instances` | `find` aplica el filtro y recorre varios documentos |
| `test_get_location_point_success` | `getLocationPoint` devuelve un `geojson.Point` |
| `test_get_location_point_timeout_recovery` | Reintenta si la API da un `GeocoderTimedOut` |
| `test_get_location_point_timeout_failure` | Si tras los reintentos no hay coordenadas, lanza `ValueError` |

Las tres últimas **no llaman a la API real**: la simulan, así que pasan sin
conexión y sin gastar peticiones.

## Lo que estas pruebas no dicen

Pasarlas es **necesario, pero no suficiente**. No cubren todos los requisitos
del enunciado: la corrección usa un conjunto ampliado de pruebas, sobre
**modelos distintos** de los de EnVivo, así que la implementación no puede
depender de nombres de campo concretos. La rúbrica del enunciado es la lista
completa de lo que se evalúa.

## Tres cosas que no hay que cambiar

- El fichero se sigue llamando `ODM.py`: las pruebas hacen `from ODM import ...`.
- `initApp` conserva sus parámetros `definitions_path`, `mongodb_uri`, `db_name`
  y `scope`: las pruebas lo llaman con esos nombres.
- `models_test.yml` se queda como está. Tus modelos van en `models.yml`.

Las pruebas trabajan sobre la base `abd_test` y la borran al empezar y al
terminar cada una, así que **no tocan tus datos**.

## La entrega

El fork es para trabajar. La entrega es la que dice el enunciado: un **ZIP**
con la carpeta del proyecto —`ODM.py`, `models.yml` y un `.json` por
colección con el volcado de la base— nombrado
`P1_G<grupo>_Nombre_Apellido-Nombre_Apellido.zip`.

## Si algo no arranca

El manual **Entorno de trabajo** de la asignatura tiene la tabla de errores
frecuentes. Los dos más habituales aquí:

- `ServerSelectionTimeoutError` → MongoDB no está levantado: `docker compose up -d mongo`.
- `ModuleNotFoundError: No module named 'ODM'` → ejecutaste `pytest` a secas.
  Tiene que ser `python -m pytest`, desde la carpeta del proyecto.
