# NetworkSentry AI — Operaciones de informática

## Título del Proyecto y Descripción General

**NetworkSentry AI** es una aplicación web para administrar infraestructura informática, ensambles de equipos, sitios y seguridad física en empresas, comercios y hogares. La informática y el soporte a estaciones de trabajo son el foco principal; cámaras IP, DVR/NVR y redes siguen disponibles.

Incluye un dashboard multisitio, inventario de computadoras, laptops, estaciones de trabajo, servidores y periféricos, ubicación por sitio/zona, planes de ensamble para compras de alta demanda, controles preliminares de compatibilidad, registro de incidencias y asistencia para problemas de compatibilidad, errores y timeouts. Las vistas de cámaras, grabaciones y SLA se conservan como funciones complementarias.

La aplicación tiene tres perfiles:

- **Super Admin:** administra sitios, ubicaciones, inventario, usuarios, canales de alerta, reglas y conexión del proveedor de IA.
- **Técnico SOC:** revisa equipos, crea planes de ensamble, solicita diagnósticos, cambia estados y documenta incidencias.
- **Cliente:** consulta solo sus instalaciones y equipos asignados.

`data.json` contiene los datos iniciales de demostración. Las modificaciones del navegador se guardan en `localStorage` y se sincronizan entre pestañas del mismo origen; la sesión se conserva en `sessionStorage`. El navegador no puede escribir de vuelta al archivo `data.json`: para compartir cambios entre usuarios o dispositivos se necesita un backend y una base de datos.

## Tecnologías y Librerías Utilizadas

- **HTML5:** estructura semántica de los portales, formularios, tablas y ventanas modales.
- **CSS3:** diseño responsivo, identidad visual, estados de dispositivos y componentes de interfaz.
- **JavaScript:** lógica de autenticación, navegación, permisos, renderizado dinámico, validaciones y persistencia local.
- **JSON:** datos iniciales de usuarios, sitios, zonas, equipos, ensambles, incidencias y configuración.
- **API Fetch:** carga asíncrona de `data.json`.
- **Web Storage API:** persistencia mediante `localStorage` y `sessionStorage`.
- **Google Gemini opcional:** API `generateContent` de Gemini, con API key de Google AI Studio. El modo Gemini se activa/desactiva desde el dashboard; en modo desactivado se usan respuestas predeterminadas locales.
- **Canvas API:** generación dinámica de las vistas simuladas de cámaras.
- **Sin frameworks de frontend:** el proyecto utiliza JavaScript nativo y no requiere React, Angular, Vue u otras librerías.
- **Python/Pandas:** no forman parte de la implementación actual. Python puede utilizarse opcionalmente para levantar un servidor HTTP local.

## Estructura del Repositorio

- **`index.html`:** contiene el dashboard, el inventario, el módulo de ensamble, asistencia IA, administración y portales de acceso.
- **`app.js`:** implementa autenticación, permisos, persistencia, inventario, ensambles, diagnósticos, grabaciones, administración y eventos.
- **`style.css`:** define los estilos generales y responsivos de todos los componentes.
- **`data.json`:** aporta los datos iniciales de demostración: usuarios, sitios, zonas, dispositivos y configuración.
- **`README.md`:** describe el proyecto, su instalación, configuración y uso básico.

## Instrucciones de Instalación y Configuración

### Requisitos previos

- Navegador moderno compatible con JavaScript y Web Storage.
- Editor opcional: Visual Studio Code.
- Para la ejecución recomendada: Python 3.10 o superior.
- Conexión a Internet solo si se utiliza una plataforma de alojamiento; no es necesaria para ejecutar el servidor local.

### Ejecución en una estación de trabajo local

1. Descargue o clone el repositorio.
2. Coloque todos los archivos del proyecto en una misma carpeta.
3. Abra una terminal o PowerShell en esa carpeta.
4. Inicie un servidor HTTP local:

   ```powershell
   python -m http.server 8000
   ```

5. Abra `http://localhost:8000` en el navegador.
6. Seleccione uno de los portales de inicio de sesión.

No se recomienda abrir `index.html` directamente mediante `file://`, porque el navegador puede impedir la carga de `data.json` con `fetch`. En ese caso, el sistema usa un modo de respaldo limitado y, normalmente, solo permite acceder con la cuenta administrativa.

### Datos y persistencia

Al iniciar por primera vez, la aplicación intenta cargar en el siguiente orden:

1. Los datos guardados en `localStorage`.
2. Los datos iniciales de `data.json`.
3. Los datos de respaldo definidos en `app.js` si `data.json` no puede cargarse.

La contraseña inicial almacenada en `data.json` se convierte a un hash didáctico al cargar la aplicación. El inventario informático (incluidas las especificaciones), los planes de ensamble, las consultas IA, los sitios, incidencias y configuración se guardan en el navegador. Para restaurar los datos iniciales:

1. Abra las herramientas de desarrollo del navegador.
2. Acceda a la sección de almacenamiento de la aplicación.
3. Elimine la clave `nsai_v3`.
4. Elimine también `nsai_sess_v3` si desea cerrar la sesión guardada.
5. Elimine `nsai_gemini_key_v1` y `nsai_gemini_active_v1` para borrar la API key y el modo Gemini de la pestaña.
6. Recargue la página.

> Nota: este mecanismo es adecuado para demostración académica. No sustituye una base de datos ni un sistema seguro de autenticación y autorización del lado del servidor.

### Configuración y límites de Google Gemini

1. Inicie sesión como Super Admin y abra **Administración → Asistente IA**.
2. Cree o consulte la clave en [Google AI Studio](https://aistudio.google.com/apikey) e introdúzcala junto con un modelo disponible, por ejemplo `gemini-3.6-flash`. Desde mayo de 2026, Google rechaza claves estándar sin restricciones; use una clave de autorización creada por AI Studio o restrinja la clave estándar a **Generative Language API**. El validador no presupone un prefijo concreto para la clave: **Probar Gemini** confirma si Google la acepta.
3. Pegue la clave y pulse **Probar y guardar clave válida**. La aplicación la valida con una solicitud real a Google y solo la guarda en la pestaña si Google responde correctamente. Si falla con `API key not valid`, la clave/proyecto está siendo rechazado por Google; revise que no esté revocada y que Generative Language API esté habilitada y permitida por las restricciones de la clave.
4. Vuelva al dashboard y pulse **Activar Google Gemini**. Para dejar de enviar consultas al proveedor, pulse **Desactivar Gemini · usar respuestas predeterminadas**.
5. En **Terminal IA → Diagnóstico de problemas informáticos**, describa el problema y marque la casilla de autorización para enviar la consulta a Google Gemini.

El nombre del modelo se guarda en la configuración local; **la API key no se guarda en `data.json` ni en `localStorage`**. La clave y el modo activo se conservan solo en memoria/`sessionStorage` de esa pestaña y desaparecen al cerrarla o al usar **Eliminar API key**. Una clave del navegador puede ser leída por scripts del mismo origen; no publique su clave en un sitio estático. Para producción, use un backend propio, restricciones de API y límites de uso. La app envía la clave en el encabezado `x-goog-api-key` a `generativelanguage.googleapis.com`.

La consulta externa se envía solo si Gemini está activado, hay una clave válida, existe conexión y el usuario da consentimiento; se comparten la descripción, la ubicación y los datos básicos del equipo seleccionado. Si se desactiva Gemini, falta clave, conexión o consentimiento, o falla la API/timeout, se muestra una respuesta predeterminada local y el motivo. La IA no tiene acceso remoto al hardware, no ejecuta reparaciones ni garantiza que una solución funcione. Confirme componentes, BIOS, RAM, conectores, dimensiones y garantía con las fichas del fabricante antes de ensamblar.

### Inventario y ensamble de equipos

- En el dashboard cree el sitio que representa la casa, local o empresa y sus zonas.
- En el panel del sitio, use **Dispositivos** para inventariar equipos de red e informática. Las estaciones informáticas incluyen fabricante/modelo, procesador, memoria, almacenamiento y sistema operativo.
- Use **Ensamble TI** para registrar cantidades, carga de trabajo, lugar de instalación y componentes. La validación señala discrepancias de socket, generación de RAM y potencia de la fuente; es una guía preliminar, no una certificación completa.
- Al terminar físicamente el ensamble, registre cada activo en **Dispositivos** con su nombre, IP y especificaciones para incorporarlo al inventario operativo.
- El Técnico SOC puede crear/editar/mover/eliminar activos informáticos; la administración completa de cámaras, equipos de red, zonas y sitios sigue reservada al Super Admin.
- Las ediciones se guardan en el navegador y se propagan a otras pestañas del mismo origen. No se comparten entre navegadores ni usuarios sin backend.

### Cuentas de demostración

| Rol | Usuario | Contraseña | Portal |
|---|---|---|---|
| Super Admin | `admin` | `Sentry#2026` | Personal / Admin |
| Técnico SOC | `tecnico1` | `Soc#Tecnico26` | Personal / Admin |
| Cliente | `cliente1` | `Cliente#2026` | Cliente |
| Cliente | `cliente2` | `Empresa#2026` | Cliente |

### Despliegue en la nube

Al ser una aplicación completamente estática, puede publicarse en GitHub Pages, Netlify, Azure Static Web Apps o un servidor web convencional.

Ejemplo con GitHub Pages:

1. Cree un repositorio y cargue `index.html`, `app.js`, `style.css`, `data.json` y `README.md`.
2. En la configuración del repositorio, abra **Pages**.
3. Seleccione la rama principal y la carpeta raíz `/`.
4. Guarde los cambios y espere la generación del sitio.

Para Netlify o Azure Static Web Apps puede arrastrarse la carpeta del proyecto al panel correspondiente o conectarse el repositorio. No configure un comando de compilación, ya que no existe una etapa de compilación.

## Guía Rápida de Uso

1. Acceda a `http://localhost:8000` o al sitio publicado.
2. Pulse **Portal Cliente** o **Portal Técnico / Admin**.
3. Inicie sesión con una cuenta de demostración.
4. En el dashboard, revise los indicadores y filtre los sitios por ubicación o equipos informáticos.
5. Pulse **Abrir panel** sobre un sitio para consultar sus datos.
6. Navegue por las pestañas disponibles según el rol:
   - consulte y mantenga el inventario informático por zona;
   - prepare planes de ensamble TI y revise alertas preliminares de compatibilidad;
   - revise la imagen en vivo simulada;
   - consulte grabaciones;
   - revise disponibilidad y SLA;
   - consulte la asistencia IA o sus respuestas locales de respaldo;
   - documente incidencias.
7. Como Super Admin, ingrese a **Administración** para crear usuarios, administrar canales, reglas y configurar el proveedor IA.
8. Como usuario autenticado, ingrese a **Mi perfil** para actualizar datos personales, redes sociales, fotografía y contraseña.
9. Use **Cerrar sesión** al finalizar. La sesión puede cerrarse automáticamente después de 15 minutos de inactividad.
