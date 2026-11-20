# Biblia RV1960 · App de consulta bíblica con React Native

Aplicación móvil hecha con **React Native** y **Expo** que consulta el texto de la Biblia
(versión **Reina-Valera 1960**) a través de la API REST pública de [bolls.life](https://bolls.life).

**Autor:** Jairo Alejandro Martínez Castro
**Materia:** Tecnologías de Desarrollo de Software · 7mo semestre
**Práctica:** Laboratorio guiado "App de consulta bíblica con React Native"

## Descripción del proyecto

La app muestra un formulario con **dos modos de consulta**:

| Modo | Qué pide | Qué muestra |
|---|---|---|
| **Capítulo completo** | Libro y capítulo | Todos los versículos del capítulo en una lista con desplazamiento (`FlatList`) |
| **Versículo único** | Libro, capítulo y versículo | Una tarjeta con la referencia (por ejemplo «Juan 3:16») y su texto |

Al abrir la app se descarga de la API la lista de los 66 libros, que se elige desde un
selector desplegable. El formulario valida los datos antes de consultar, se muestra un
indicador de carga mientras llega la respuesta y los errores (capítulo o versículo
inexistente, sin conexión) se informan con un mensaje sin cerrar la app.

### Funcionalidades

- Selector de modo (capítulo completo / versículo único) que reinicia el formulario al cambiar.
- Lista de libros cargada desde la API (66 libros, de Génesis a Apocalipsis).
- Validación del formulario con mensajes claros:
  - debe elegirse un libro;
  - el capítulo debe ser un entero mayor a 0 y no exceder los capítulos del libro (por ejemplo, «Juan tiene 21 capítulos.»);
  - en el modo versículo, el versículo debe ser un entero mayor a 0.
- Indicadores de carga (`ActivityIndicator`) al iniciar y al consultar.
- Manejo de errores de red y de datos inexistentes.
- Limpieza del texto de la API: se eliminan etiquetas `<br>` y otras etiquetas HTML y se normalizan los espacios repetidos (por ejemplo, en Salmos 23).

## Tecnologías

- [React Native](https://reactnative.dev/) 0.86 y [React](https://react.dev/) 19
- [Expo](https://expo.dev/) SDK 57
- JavaScript con archivos `.jsx` (sin TypeScript)
- `fetch` y `async/await` para consumir la API REST
- Hooks de React: `useState`, `useEffect`

## Estructura del proyecto

```
biblia-app/
├── App.js                         # Pantalla principal y punto de entrada
├── app.json                       # Configuración de Expo (nombre, íconos, paquete Android)
├── package.json
├── android/                       # Proyecto nativo Android (build nativo)
└── src/
    ├── api/
    │   └── biblia.js              # Llamadas a la API con fetch
    ├── components/
    │   ├── FormularioConsulta.jsx # Formulario controlado con validación
    │   ├── SelectorLibro.jsx      # Lista desplegable de libros
    │   ├── ListaVersiculos.jsx    # FlatList con los versículos de un capítulo
    │   └── TarjetaVersiculo.jsx   # Un solo versículo
    ├── data/
    │   └── ejemplo.js             # Datos de prueba usados al construir la interfaz sin internet
    └── utils/
        └── texto.js               # Limpieza del texto que devuelve la API
```

Los componentes de interfaz están en archivos `.jsx` y los módulos sin interfaz
(`api`, `utils`, `data`) en `.js`.

## API utilizada

Servicio público [bolls.life](https://bolls.life), traducción `RV1960`:

| Uso | Petición | Respuesta |
|---|---|---|
| Lista de libros | `GET /get-books/RV1960/` | Arreglo de 66 objetos `{ bookid, chronorder, name, chapters }` |
| Capítulo completo | `GET /get-text/RV1960/{bookId}/{chapter}/` | Arreglo `{ pk, verse, text }` (vacío si el capítulo no existe) |
| Versículo único | `GET /get-verse/RV1960/{bookId}/{chapter}/{verse}/` | Un objeto `{ pk, verse, text }` (error 404 si no existe) |

## Cómo ejecutar el proyecto

### Requisitos

- [Node.js](https://nodejs.org/) (versión LTS)
- La app **Expo Go** en un celular Android o iOS (en la misma red Wi-Fi que la computadora), o un emulador/simulador
- Conexión a internet para consultar la API

### Pasos

```bash
# 1. Clonar el repositorio
git clone https://github.com/JairoMartinezUM/biblia-app.git
cd biblia-app

# 2. Instalar dependencias
npm install

# 3. Iniciar el servidor de desarrollo
npx expo start
```

Escanea el código QR con Expo Go (Android) o con la cámara (iOS). También puedes presionar
`a` para abrir un emulador de Android o `i` para el simulador de iOS.
Si el celular no conecta (por ejemplo, en una red restringida), usa `npx expo start --tunnel`.

Para compilar e instalar la versión nativa de Android (requiere Android Studio y un dispositivo
o emulador): `npx expo run:android`.

## Pruebas realizadas

Casos de la guía verificados contra la API y el código:

| Caso | Resultado |
|---|---|
| Lista de libros | La API devuelve 66 libros; Juan (id 43) tiene 21 capítulos |
| Capítulo completo: Juan 3 | 36 versículos |
| Capítulo completo: Salmos 23 | 6 versículos, sin etiquetas `<br>` tras la limpieza |
| Capítulo inexistente (Juan 99) | La API devuelve `[]`; la app muestra un mensaje de error |
| Versículo inexistente (Juan 3:99) | La API responde 404; la app muestra un mensaje de error |
| Libro sin elegir / capítulo fuera de rango / capítulo 0 | El formulario muestra el mensaje de validación correspondiente |
| Empaquetado de la app | `npx expo export --platform android` genera el bundle sin errores |

## Requisitos de la práctica cubiertos

| Criterio | Cómo se cumple |
|---|---|
| El proyecto se ejecuta sin errores | Se ejecuta con Expo Go o con el build nativo de Android |
| Modo «Capítulo completo» | `ListaVersiculos` con `FlatList`, conectado a `obtenerCapitulo` |
| Modo «Versículo único» | `TarjetaVersiculo`, conectado a `obtenerVersiculo` |
| Validación del formulario | `validar()` en `FormularioConsulta.jsx` |
| Indicador de carga y manejo de errores | `ActivityIndicator` y bloques `try / catch / finally` en `App.js` |
| Código organizado en componentes `.jsx` | Estructura de carpetas descrita arriba |

## Licencia

Distribuido bajo la licencia indicada en el archivo [LICENSE](LICENSE).
