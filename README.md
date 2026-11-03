# Inventario Forestal Nacional (IFN)

Software de apoyo para el registro y la consulta de la información del Inventario Forestal Nacional del IDEAM. Es el trabajo final de semestre del curso **Programación I** de la Universidad de Santander.

## Contexto

El IDEAM (Instituto de Hidrología, Meteorología y Estudios Ambientales) adelanta el Inventario Forestal Nacional. Para hacerlo, el territorio colombiano se divide en **conglomerados**, y cada uno se asigna a una **brigada**. Una brigada está formada por un líder, profesionales de distintas ramas y coinvestigadores locales.

Durante cada inspección, la brigada sigue estos pasos:

1. Se desplaza hasta el centroide del conglomerado.
2. Cuenta los árboles de cada subparcela según su diámetro: fustales, latizales y brinzales.
3. Toma muestras de especies vegetales con su nombre común y su uso (medicinal, alimenticio o maderable).
4. Envía las muestras al laboratorio, que determina el nombre científico.

Este programa permite registrar ese proceso en orden y consultar toda la información de cualquier inspección.

## Requisitos del proyecto

| Requisito | Cómo se cumple |
|---|---|
| Consultar cualquier inspección con los datos del conglomerado, la brigada, el conteo y el muestreo | Opción **7. Consultar inspección** |
| Arquitectura de tres capas | Capa de datos, capa lógica y capa de presentación, separadas dentro del código |
| Persistencia en archivos de texto plano | Archivos `.csv` |
| Interacción con el usuario | Interfaz con `ipywidgets` dentro de Google Colab |

## Funcionalidades

El menú sigue el orden del proceso del IFN. Cada paso se habilita solo cuando existen los datos que necesita.

1. **Registrar brigada.** Se registran el líder (nombre completo y profesión), uno o más profesionales (nombre completo y profesión) y uno o más coinvestigadores locales (nombre completo).
2. **Registrar conglomerado.** Se eligen el departamento y el municipio de una lista y se asigna una brigada. Las coordenadas del centroide se proponen con las de la cabecera municipal y se pueden ajustar con la lectura del GPS.
3. **Registrar inspección.** Se registran el conglomerado inspeccionado y la fecha.
4. **Registrar conteo.** Se registran los fustales, latizales y brinzales de cada subparcela. Solo aparecen las subparcelas que todavía no tienen conteo.
5. **Registrar muestra.** Se registran el nombre común y el uso de la especie. La muestra queda pendiente de clasificación.
6. **Clasificar muestra.** Se registran el género y la especie que determina el laboratorio.
7. **Consultar inspección.** Se muestra toda la información relacionada con una inspección.

### Ayudas para el usuario

- **Barra de resumen** con la cantidad de registros de cada tipo y las muestras sin clasificar.
- **Instrucciones** del paso actual, debajo del menú.
- **Panel de requisitos** que marca cada dato como `[Listo]` o `[Falta]` mientras se llena el formulario. El botón **Guardar** se activa solo cuando todos los requisitos están completos.
- **Listas desplegables** en los campos que piden información específica: departamento, municipio, profesión, uso, subparcela y los códigos existentes. Cada código va acompañado de una descripción, por ejemplo `C1 - Quibdó, Chocó`.
- **Mensajes de confirmación** que indican cuál es el siguiente paso del proceso.

## Arquitectura

El programa se ejecuta en una sola celda de forma procedural, en este orden: capa de datos, capa lógica y capa de presentación.

### Capa de datos

Lee y escribe los archivos `.csv`.

| Función | Descripción |
|---|---|
| `guardar(nombre)` | Escribe en su archivo `.csv` la lista de registros indicada |
| `hayDatos()` | Indica si existe al menos un registro |
| `cargarDatosPrueba()` | Carga un conjunto de datos de ejemplo |

Al iniciar, el programa carga los archivos existentes y crea `municipios.csv` si no existe.

### Capa lógica

Contiene las reglas del inventario y las búsquedas.

| Función | Descripción |
|---|---|
| `buscar` / `filtrar` | Devuelven el primer registro o todos los registros que cumplen una condición |
| `pendientes()` | Lista las muestras sin nombre científico |
| `nuevoCodigo(nombre, letra)` | Genera el siguiente código (B1, C1, I1, M1…) |
| `textoValido` / `personaValida` | Validan textos y nombres completos |
| `formatoCientifico(genero, especie)` | Da formato al nombre científico (`Cedrela odorata`) |
| `municipiosDe` / `ubicacionMunicipio` | Consultan los municipios de un departamento y sus coordenadas |
| `coordenadasEnMunicipio` | Verifica que el centroide esté dentro de la zona del municipio |
| `subparcelasLibres(inspeccion)` | Lista las subparcelas que aún no tienen conteo |
| `registrar` / `clasificar` | Agregan o actualizan registros y los guardan |
| `consultarInspeccion(codigo)` | Reúne toda la información de una inspección |

### Capa de presentación

Maneja la interfaz con `ipywidgets`.

| Función | Descripción |
|---|---|
| `clicMenu` | Muestra el formulario del paso elegido |
| `clicAccion` | Guarda o consulta según el paso actual |
| `clicAgregar` | Agrega profesionales y coinvestigadores a la brigada |
| `actualizarRequisitos` | Actualiza el panel de requisitos y habilita el botón Guardar |
| `actualizarMenu` | Habilita los pasos del menú y actualiza el resumen |
| `cargarMunicipios` / `llenarCoordenadas` / `cargarSubparcelas` | Actualizan las listas que dependen de otra |
| `limpiarCampos` / `cargarLista` / `opcionesDe` | Limpian el formulario y cargan las opciones de las listas |

## Archivos de datos

| Archivo | Columnas |
|---|---|
| `brigadas.csv` | brigada, lider, profesionales, coinvestigadores |
| `conglomerados.csv` | conglomerado, departamento, municipio, latitud, longitud, brigada |
| `inspecciones.csv` | inspeccion, conglomerado, fecha |
| `conteos.csv` | inspeccion, subparcela, fustales, latizales, brinzales |
| `muestras.csv` | codigo, inspeccion, nombreComun, uso, nombreCientifico |
| `municipios.csv` | departamento, municipio, latitud, longitud |

Los archivos se crean automáticamente en la carpeta de trabajo de Colab la primera vez que se guarda un registro.

## Cómo ejecutarlo

1. Abra `IFN_Inventario_Forestal.ipynb` en [Google Colab](https://colab.research.google.com/), con el botón de arriba o subiendo el archivo.
2. Ejecute la única celda del notebook.
3. Empiece por **1. Registrar brigada**, o pulse **Cargar datos de prueba** para ver el programa con información de ejemplo.

No necesita instalar nada. Colab ya incluye `ipywidgets`, y el resto de librerías (`csv` y `datetime`) son parte de Python.

> Colab conserva los archivos `.csv` mientras dure la sesión. Para empezar desde cero, borre esos archivos desde el panel de archivos o use **Entorno de ejecución → Desconectar y borrar el entorno de ejecución**.

## Reglas de validación

- Los nombres de personas deben tener al menos nombre y apellido. Solo se aceptan letras, espacios, guiones, apóstrofes y puntos, con un máximo de 60 caracteres.
- El centroide debe estar a menos de 0,5 grados (unos 55 km) de la cabecera del municipio elegido.
- La fecha de la inspección no puede ser posterior a la fecha actual.
- Cada conglomerado tiene 5 subparcelas, y cada una se puede contar una sola vez por inspección.
- Los conteos de árboles van de 0 a 1.000 por categoría.
- El género y la especie deben ser una palabra cada uno. El nombre científico se guarda con el formato de la nomenclatura binomial.

## Limitaciones

- `municipios.csv` incluye las capitales de los 33 departamentos y algunos municipios con actividad forestal, unos 55 en total. Para agregar más municipios basta con añadir filas al archivo.
- Las coordenadas de las cabeceras municipales son aproximadas y sirven como punto de partida, no como dato oficial.
- El programa no permite editar ni borrar registros ya guardados.

## Autor

**Andrés Felipe Pabón Barbosa**
Programación I, Universidad de Santander
Docente: Carlos Carrascal
