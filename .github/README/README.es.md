# 1P1A (One Page One App) & OJapp Dynamic PWA Suite

> Convierte una página, un directorio o un sitio completo en una Progressive Web App (PWA) independiente mediante un script ligero del lado del cliente y un manifiesto Data URL generado dinámicamente.

---

## 🌟 Descripción general

**1P1A (One Page One App)** es un enfoque PWA del lado del cliente que elimina la necesidad de crear y mantener un archivo `manifest.json` estático para cada página o herramienta.

Añade un script de OJapp al `<head>` de la página y este generará dinámicamente un Web App Manifest como URL `data:application/manifest+json`. Según el modo seleccionado, los visitantes pueden añadir a su pantalla de inicio una página individual, un grupo de directorios o un sitio completo, con su propio nombre, descripción, icono, identidad, URL de inicio, ámbito de navegación y presentación de instalación.

Los scripts principales no requieren proceso de compilación, registro de usuarios, API externa ni generación del manifiesto en el servidor.

---

## 🚀 Funciones principales

- **Sin compilación ni backend:** funciona en alojamientos estáticos como Cloudflare Pages, GitHub Pages, Vercel y servidores web tradicionales.
- **Manifiestos Data URL dinámicos:** genera el Web App Manifest en el navegador durante la ejecución.
- **Tres estructuras de aplicación:** admite aplicaciones por página (1P1A), por directorio (1G1A) y para todo el sitio (1S1A).
- **Gestión opcional de consultas:** añade la cadena de consulta actual a `id`, `start_url` y `scope` cuando está activada.
- **Metadatos personalizados:** configura el título, la descripción, el icono, la identidad, la URL de inicio y el ámbito de navegación mediante etiquetas meta.
- **Presentación de instalación:** añade una descripción específica y una o varias capturas de pantalla (hasta cinco) sin mantener un manifiesto estático.
- **Ejecución totalmente del lado del cliente:** los scripts Free funcionan sin cuentas de usuario ni dependencias de API externas.

---

## 💻 Inicio rápido y estructuras de aplicación

OJapp lee sus etiquetas meta cuando se ejecuta el script. Coloca todas las etiquetas meta de OJapp **antes** de la etiqueta script.

### 1. 1P1A: One Page One App

Convierte la página actual —o una instancia de esa página configurada mediante una consulta— en una aplicación en la pantalla de inicio del usuario.

```html
<!-- Los ajustes opcionales deben aparecer antes del script -->
<meta name="ojapp:query" content="true">
<meta name="ojapp:title" content="Mi herramienta personalizada">
<meta name="ojapp:icon" content="/icon.png">

<script src="https://ojapp.app/js/ojapp_1p1a.js"></script>
```

Sin etiquetas meta opcionales, se utilizan automáticamente la URL, el título y el icono disponible de la página.

### 2. 1S1A: One Site One App

Convierte todo el origen en una única aplicación unificada.

```html
<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

De forma predeterminada, `id`, `start_url` y `scope` utilizan la raíz del origen (`/`).

### 3. 1G1A: One Group One App

Convierte un directorio como `/dashboard/`, `/tools/` o `/docs/` en su propia aplicación dentro del mismo origen.

```html
<meta name="ojapp:id" content="/dashboard/">
<meta name="ojapp:start-url" content="/dashboard/">
<meta name="ojapp:scope" content="/dashboard/">

<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

Cada grupo de directorios puede usar un ID y un scope diferentes, lo que permite varias aplicaciones de grupo bajo un mismo origen sin archivos de manifiesto ni procesos de compilación separados.

---

## 🔗 Aplicaciones configuradas mediante consultas

Activa la gestión de consultas con:

```html
<meta name="ojapp:query" content="true">
```

Por ejemplo:

```text
/timer/?time=5&mode=down&seconds=on&icon=blue
```

OJapp incluye la cadena de consulta actual en los siguientes valores del manifiesto generado:

- `id`
- `start_url`
- `scope`

Esto permite que la URL almacene el estado seleccionado de una aplicación, mientras `id` distingue las instancias configuradas y `start_url` restaura ese estado al iniciar la aplicación.

Los navegadores procesan los campos del manifiesto según su implementación de plataforma. En particular, un navegador puede normalizar o eliminar la consulta del `scope` procesado. OJapp sigue escribiendo la consulta configurada en los tres campos y deja que el navegador realice esa normalización.

Si la página crea o modifica la consulta después de la carga inicial, navega a la URL final o vuelve a cargarla antes de pedir al usuario que la añada a la pantalla de inicio. Un cambio realizado únicamente con History API puede no actualizar la URL capturada por el flujo **Añadir a pantalla de inicio** de iOS.

---

## 🖼️ Presentación de instalación

OJapp puede personalizar la descripción y las capturas de pantalla mostradas en las interfaces de instalación compatibles.

### Descripción de instalación

Utiliza `ojapp:description` cuando la interfaz de instalación necesite un texto distinto de la descripción de búsqueda de la página.

```html
<meta
  name="ojapp:description"
  content="Instala esta herramienta para acceder rápidamente desde tu pantalla de inicio."
>
```

OJapp utiliza el siguiente orden de prioridad:

1. `ojapp:description`
2. `<meta name="description">` estándar
3. Ninguna `description` en el manifiesto si no existe ninguna de las anteriores

Así se mantiene separado el texto orientado a buscadores del mensaje de instalación.

### Una captura de pantalla

Utiliza la etiqueta original sin numerar para una sola imagen.

```html
<meta
  name="ojapp:screenshot"
  content="/images/install.png"
>
```

### Varias capturas de pantalla

Utiliza etiquetas numeradas para añadir hasta cinco imágenes.

```html
<meta name="ojapp:screenshot-1" content="/images/install-1.png">
<meta name="ojapp:screenshot-2" content="/images/install-2.png">
<meta name="ojapp:screenshot-3" content="/images/install-3.png">
```

Si existe al menos una etiqueta numerada, el conjunto numerado tiene prioridad y se ignora la etiqueta `ojapp:screenshot` sin numerar.

Todas las capturas del conjunto deben usar la misma relación de aspecto. Se recomienda una **imagen cuadrada 1:1**, ya que se visualiza fácilmente tanto en interfaces de instalación de escritorio como móviles. OJapp asigna las capturas seleccionadas a `wide` en escritorio y a `narrow` en Android; el navegador controla el diseño final.

---

## 🛠️ Referencia de metadatos

| Etiqueta meta | Se aplica a | Descripción | Valor predeterminado / Alternativa |
| :--- | :--- | :--- | :--- |
| `ojapp:title` | 1P1A / 1S1A | Define el nombre de la aplicación | 1P1A: `<title>` de la página; 1S1A: nombre del host |
| `ojapp:description` | 1P1A / 1S1A | Define la descripción de instalación del manifiesto | Meta descripción estándar de la página; en caso contrario, se omite |
| `ojapp:icon` | 1P1A / 1S1A | Define la URL del icono | Icono disponible de la página y, después, icono predeterminado de OJapp |
| `ojapp:screenshot` | 1P1A / 1S1A | Define una captura de instalación | Se omite |
| `ojapp:screenshot-1` a `ojapp:screenshot-5` | 1P1A / 1S1A | Define hasta cinco capturas; el conjunto numerado tiene prioridad | Se omiten |
| `ojapp:query` | 1P1A / 1S1A | Establécelo en `"true"` para incluir la consulta actual en `id`, `start_url` y `scope` | Desactivado; consulta eliminada |
| `ojapp:id` | 1S1A / 1G1A | Define la identidad de la aplicación en el manifiesto | Raíz del origen |
| `ojapp:start-url` | 1S1A / 1G1A | Define la URL abierta desde la pantalla de inicio | Raíz del origen |
| `ojapp:scope` | 1S1A / 1G1A | Define el ámbito de navegación | Raíz del origen |
| `ojapp:exclude` | 1P1A / 1S1A | Establécelo en `"true"` para desactivar OJapp en la página | Desactivado |

Todos los valores personalizados de `id`, `start_url` y `scope` deben resolverse en el mismo origen que la página actual.

---

## 📱 Comportamiento según la plataforma

### iOS / iPadOS (Safari)

Las entradas de la pantalla de inicio configuradas mediante consultas se han verificado en iPhone. Varias entradas creadas desde la misma página pueden conservar estados de inicio, nombres e iconos distintos.

El icono instalado también puede depender de `apple-touch-icon`, por lo que las páginas que seleccionen un icono dinámicamente deben establecerlo antes de ejecutar el script de OJapp.

### Android (Chrome)

OJapp incluye la consulta en los valores generados de `id`, `start_url` y `scope`. Chrome puede normalizar el ámbito de navegación procesado de acuerdo con la especificación Web App Manifest. La identidad de múltiples instalaciones basadas en consultas puede variar según la versión del navegador y de la plataforma, así que prueba el comportamiento deseado en el dispositivo Android de destino.

---

## 🧭 Elegir un modo

| Modo | Unidad de aplicación | Identidad / inicio / scope predeterminados | Uso habitual |
| :--- | :--- | :--- | :--- |
| **1P1A** | Página actual | Ruta de la página actual | Herramientas, productos, artículos, perfiles |
| **1G1A** | Grupo de directorios | Ruta de directorio explícita | Paneles, documentación, herramientas agrupadas |
| **1S1A** | Sitio completo | Raíz del origen `/` | PWA convencional para todo el sitio |

---

## OJapp FREE

OJapp FREE ofrece una forma sencilla de implementar los patrones de diseño PWA 1P1A (One Page. One App.) y 1S1A (One Site. One App.).

Para el uso normal, OJapp FREE se proporciona mediante los scripts oficiales alojados en:

https://ojapp.app/

El runtime de producción de OJapp FREE no se distribuye como archivo JavaScript desde este repositorio.

Este repositorio puede incluir código fuente de referencia en archivos `.txt` para aprendizaje, investigación, modificación e implementación independiente.

Si solo quieres utilizar OJapp FREE, usa el script oficial alojado que se describe en la documentación.

---

## Licencia

Salvo que se indique lo contrario, el código fuente de este repositorio se publica bajo la licencia MIT.

Consulta el archivo `LICENSE` para obtener más información.

La licencia MIT de este repositorio solo se aplica al código fuente publicado realmente en él.

El runtime alojado de OJapp FREE proporcionado desde `ojapp.app` y OJapp PRO son independientes de este repositorio y no están cubiertos por su licencia MIT.

OJapp PRO es software comercial propietario.

---

## 1P1A y UDA son conceptos de diseño

**1P1A (One Page. One App.)** y **UDA (User Defined App)** son conceptos de diseño PWA, no productos.

1P1A describe un enfoque en el que el límite de la aplicación se define a nivel de página en lugar de abarcar todo el sitio web.

UDA amplía esta idea permitiendo que los usuarios definan una aplicación mediante un estado específico de la URL, como los parámetros de consulta.

OJapp es una implementación y un servicio desarrollados en torno a estos conceptos.

La licencia MIT de este repositorio solo se aplica al código fuente publicado aquí. No se aplica a los propios conceptos 1P1A o UDA.

---

## 🤝 Comunidad y comentarios

Las preguntas, resultados de pruebas, casos límite y solicitudes de funciones son bienvenidos en este repositorio.
Las implementaciones de referencia pueden diferir de la versión de producción actual de OJapp FREE y no pretenden reflejar todas las actualizaciones del runtime oficial alojado.

- **Sitio web / Utilidades:** [OJapp 1P1A](https://ojapp.app/one-page-one-app/en/)
- **Desarrollador:** OJapp / Ojach
