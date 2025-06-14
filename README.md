# 📦 Azure Data Factory - Pipeline1

## 🧩 Descripción General

Este pipeline (`pipeline1`) implementa un flujo ETL dinámico sobre archivos CSV almacenados en Azure Data Lake (Blob Storage). Está diseñado para identificar archivos actualizados el día de la ejecución, diferenciarlos según su tipo (`Retail` o `Student`), y moverlos de la zona **Landing** hacia la zona **Bronze**, manteniendo el formato `.txt`.

El flujo permite el procesamiento controlado y escalable de archivos delimitados bajo una estructura modular, usando actividades como `Get Metadata`, `Filter`, `ForEach`, `IfCondition` y `Copy`.

---

## 📌 Parámetros

| Nombre           | Tipo     | Descripción                          | Valor por defecto     |
|------------------|----------|--------------------------------------|------------------------|
| `p_container`    | string   | Contenedor en el Data Lake           | `datalake`             |
| `p_directory`    | string   | Directorio de entrada (Landing)      | `landing`              |
| `p_file_name`    | string   | Nombre del archivo principal         | `Online Retail Data Set.csv` |

---

## 🧠 Lógica del Pipeline

### 1. `Get Metadata1`
- Obtiene la lista (`childItems`) y fecha de modificación (`lastModified`) de los archivos en la carpeta `landing`.

### 2. `Filter2`
- Filtra solo los archivos que contienen `"Retail"` en su nombre.

### 3. `ForEach2`
- Itera sobre los archivos `"Retail"` y:
  - Usa `Get Metadata2` para leer `lastModified`.
  - Si el archivo fue modificado **hoy**, se activa `Copy data1_copy1_copy1` para moverlo a la zona `bronze` y guardar el nombre en la variable `name_silver`.

### 4. `ForEach1`
- Usa un filtro inverso:
  - Si el archivo **NO contiene "Retail"**, pero sí contiene `"Student"` y termina en `.csv`, se copia con `Copy data1`.
  - Si el archivo **SÍ contiene "Retail"**, se ejecuta otra copia (`Copy data1_copy1`) directamente.

### 5. `Copy data Silver`
- Finalmente, toma el archivo cargado recientemente y lo sobrescribe en la zona `bronze` con el nombre definido en `name_silver`.

---

## 🧪 Validaciones y Condiciones

- `If Condition1`: Distingue entre archivos `"Retail"` y `"Student"`.
- `If Condition2`: Verifica si el archivo `"Retail"` fue modificado el día actual (`utcNow()`).
- Archivos copiados se renombran con extensión `.txt` para formato uniforme.

---

## 📂 Estructura Esperada en el Data Lake
