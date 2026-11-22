# Análisis Técnico, Requerimientos, Supuestos y Registro de Decisiones de Arquitectura (ADR)

**Proyecto:** Aplicación Móvil de Control Electoral para Organización Política  
**Plataforma:** React Native / Expo (APK instalable en Android)  
**Arquitectura:** Clean Architecture  
**Fecha:** 2026-11-21

### Datos del Autor / Equipo

| Campo | Detalle |
| :--- | :--- |
| **Integrante** | Kevin Alexander Amagua Valenzuela |

---

## 1. Requerimientos del Sistema

### 1.1 Requerimientos Funcionales (RF)

| Código | Necesidad que lo origina | Descripción del requerimiento |
| :--- | :--- | :--- |
| **RF-01** | Acceso de los usuarios | Prohibir el auto-registro libre; el coordinador provincial crea y habilita cuentas de coordinadores de recinto, y cada coordinador de recinto crea y habilita cuentas de veedores. |
| **RF-02** | Acceso de los usuarios | Exigir al registrar un usuario cédula ecuatoriana validada (dígito verificador y sin errores de digitación), nombres y apellidos completos, teléfono y correo electrónico. |
| **RF-03** | Acceso de los usuarios | Obligar a todo usuario nuevo a cambiar la contraseña inicial en su primer ingreso para que solo el titular conozca su clave definitiva. |
| **RF-04** | Acceso de los usuarios | Verificar que el correo electrónico ingresado es real y pertenece al titular antes de considerar la cuenta habilitada, permitiendo recuperación autónoma de contraseña sin depender del coordinador provincial. |
| **RF-05** | Organización del territorio y del personal | Precargar en la aplicación la provincia y cantón elegidos, sus recintos electorales (cantón, parroquia, nombre del recinto y número de JRV/mesa) y las cinco organizaciones políticas con sus candidatos a alcaldía y prefectura. |
| **RF-06** | Organización del territorio y del personal | Permitir al coordinador provincial consultar los recintos de su jurisdicción y registrar nuevos recintos cuando se requiera. |
| **RF-07** | Organización del territorio y del personal | Garantizar un único coordinador por recinto electoral y permitir asignar a un veedor una o varias mesas dentro de su recinto sin límite de mesas. |
| **RF-08** | Organización del territorio y del personal | Permitir al coordinador de recinto reemplazar en el momento a un veedor ausente por otro veedor disponible, incluyendo habilitar en el acto a una persona sin afiliación previa que ofrezca el servicio afuera del recinto. |
| **RF-09** | Registro de los resultados | Permitir al veedor transcribir dos actas por cada mesa asignada (alcaldía y prefectura), ingresando votos de las cinco organizaciones políticas, votos nulos, votos en blanco y total de sufragantes. |
| **RF-10** | Registro de los resultados | Validar en tiempo real al ingresar o editar datos que ninguna organización política supere el total de sufragantes y comprobar la coherencia numérica de las sumas según los supuestos adoptados. |
| **RF-11** | Registro de los resultados | Exigir una fotografía legible del acta (con revisión de calidad/nitidez y vista previa con zoom antes de confirmar) y registrar la ubicación desde donde se realizó el registro, consultables al revisar el acta. |
| **RF-12** | Registro de los resultados | Permitir al veedor corregir las actas de sus mesas asignadas y al coordinador de recinto corregir cualquier acta perteneciente a su recinto. |
| **RF-13** | Registro de los resultados | Indicar visualmente en todo momento si cada operación está *en proceso*, *se guardó* o *falló*, evitando registros duplicados por reintentos o abandonos. |
| **RF-14** | Confidencialidad de la información | Restringir la visualización y edición por rol: cada veedor accede solo a sus mesas asignadas, cada coordinador de recinto solo a su recinto y el coordinador provincial a su jurisdicción. |
| **RF-15** | Seguimiento del escrutinio | Mostrar al coordinador provincial cuántas mesas reportaron y cuántas faltan por recinto y en total, el conteo acumulado de votos por candidato a alcaldía y prefectura (por recinto y total) conforme ingresan las actas, y la ubicación (en mapa y coordenadas) desde donde se registró cada acta al revisarla. |
| **RF-16** | Conectividad el día de la elección | Permitir registrar y respaldar actas sin señal en recintos rurales y sincronizar automáticamente la información pendiente cuando el dispositivo recupere conexión, sin perder ni duplicar registros. |
| **RF-17** | Estabilidad de la aplicación | Detectar automáticamente cierres inesperados y errores que bloqueen tareas (como fallos al subir una foto), registrando dispositivo, versión de Android y pantalla afectada, guardando el reporte sin conexión y enviándolo al recuperar red. |

### 1.2 Requerimientos No Funcionales (RNF)

| Código | Necesidad que lo origina | Descripción del requerimiento |
| :--- | :--- | :--- |
| **RNF-01** | Restricciones del proyecto | Desarrollar la aplicación en React Native y entregar un archivo APK instalable y funcional en dispositivos Android. |
| **RNF-02** | Restricciones del proyecto | Organizar el código siguiendo Clean Architecture (`domain`, `data`, `presentation`) y respetar la línea base de requerimientos, supuestos y ADR acordada por el equipo. |
| **RNF-03** | Rúbrica y autoría del proyecto | Documentar cada decisión de arquitectura en su archivo individual dentro de `docs/adr/` antes de instalar dependencias o implementar el código correspondiente. |
| **RNF-04** | Acceso de los usuarios | Diseñar flujos simples, claros y tolerantes a errores pensando en veedores y coordinadores mayores o con poca experiencia en el uso de teléfonos móviles. |
| **RNF-05** | Rúbrica de evaluación | Aplicar los colores de la organización política elegida y brindar retroalimentación visual clara en estados *en proceso*, *guardado*, *falló* y validaciones usando **Lottie** y **Reanimated**, evitando modales genéricos del sistema. |
| **RNF-06** | Estabilidad y conectividad | Mantener la integridad de los datos locales y sesiones ante cortes intermitentes de red o cierres inesperados, y registrar telemetría de fallos con contexto de dispositivo, versión de Android y pantalla. |

---

## 2. Supuestos adoptados

| Código | Ambigüedad que resuelve | Supuesto adoptado y justificación |
| :--- | :--- | :--- |
| **S-01** | Provincia, cantón, organizaciones políticas y paleta visual de la organización no especificados en el enunciado. | Se elige **[Pichincha / Quito o jurisdicción elegida]** (con un subconjunto manejable de recintos y mesas/JRV), junto con una organización política con su paleta cromática **[colores principales/secundarios]** y las cinco listas/candidatos para alcaldía y prefectura precargados al iniciar `expo-sqlite`. Así los datos de prueba son verosímiles, los cálculos del *exit poll* son verificables y la identidad visual es consistente. |
| **S-02** | Regla exacta de cuadre numérico entre votos de candidatos, nulos, blancos y total de sufragantes en el acta. | Se valida mediante `Zod` que ningún candidato supere el `total_sufragantes`, que no existan valores negativos y que la suma `votos_candidatos + votos_nulos + votos_blancos` coincida con el `total_sufragantes` (o, si el documento físico presenta descuadre aritmético, exigir confirmar explícitamente el estado `observada` para revisión del coordinador). Esto evita cifras imposibles desde el ingreso local sin conexión. |
| **S-03** | Manejo de autoría y actas previas al reemplazar en el momento a un veedor ausente por otro (incluso sin afiliación previa). | Al reemplazar a un veedor ausente, la asignación activa de la(s) mesa(s) pasa al nuevo veedor habilitado por el coordinador de recinto (revocando el acceso previo del veedor saliente a esas mesas), pero cada acta conserva su historial (`registrado_por`, `corregido_por`, `updated_at`). De esta forma se asegura continuidad operativa sin perder trazabilidad. |
| **S-04** | Comportamiento al registrar un acta si el GPS tarda en responder en aulas cerradas o si el usuario deniega permisos de ubicación. | Al confirmar un acta, `expo-location` intenta capturar coordenadas con un tiempo máximo de espera de 8 segundos. Si no se obtiene lectura GPS o el permiso fue denegado, no se bloquea el guardado del acta: se guarda el estado (`captured`, `timeout_no_gps` o `permission_denied`) con marca de tiempo y opción de recaptura, visible al revisar el acta junto con su ubicación en mapa cuando exista. |
| **S-05** | Prevención de registros duplicados ante reintentos por falta de señal o cierres de aplicación durante la sincronización. | Cada acta, evidencia y operación en la cola local de `expo-sqlite` se crea con un `UUID` idempotente en el teléfono y una marca de actualización (`updated_at`). Al reconectar mediante `NetInfo`, Supabase ejecuta una operación *upsert* sobre ese `UUID`, garantizando que los reintentos actualicen el registro sin crear duplicados. |
| **S-06** | Alcance de los datos enviados en los reportes automáticos de fallos y privacidad de credenciales. | Los eventos enviados a Sentry incluyen modelo de dispositivo, versión de Android, pantalla/ruta actual, rol activo y detalle técnico de la excepción (encolándose localmente sin internet), excluyendo contraseñas y tokens. Esto permite diagnosticar fallos reales en campo y demostrar un error provocado en la defensa sin exponer datos sensibles. |

---

## 3. Registro de Decisiones de Arquitectura (ADR)

### ADR-001: Usar Expo Router para la navegación basada en archivos

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-01`, `RF-03`, `RF-14`, `RNF-02`, `RNF-04`, `S-01`.
* **Contexto:** La aplicación tiene tres perfiles de usuario con permisos y pantallas distintas (`coordinador provincial`, `coordinador de recinto` y `veedor`), además de un flujo obligado de cambio de contraseña en el primer inicio de sesión y pantallas para registro o corrección de actas por mesa. Muchos usuarios son personas mayores o con poca experiencia en el celular, por lo que la navegación debe ser predecible y responder bien al botón atrás de Android. El proyecto se desarrolla en React Native con Expo y Clean Architecture.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **Expo Router** | Viene incluido en la plantilla de Expo sin añadir dependencias externas. Estructura las pantallas por grupos de rutas (`auth`, `provincial`, `recinto`, `veedor`) y facilita redirigir según rol o cambio obligatorio de clave. | Obliga a respetar una convención fija de carpetas donde todo archivo dentro de `app/` se vuelve ruta, exigiendo mantener `domain/`, `data/` y componentes fuera de `app/`. |
| **React Navigation configurado manualmente** | Control explícito sobre cada stack o tab navigator en código TypeScript. | Requiere instalar y configurar manualmente varios paquetes de navegación que Expo ya resuelve y mantiene las rutas menos visibles en la estructura de carpetas. |

* **Decisión:** Se elige **Expo Router** organizando grupos de rutas separados por rol y controlando la redirección inicial según sesión, rol activo y verificación del primer cambio de clave.
* **Consecuencias:** Se aprovecha la navegación incluida en Expo sin añadir librerías extra y se mantiene una separación clara de pantallas por rol. Como desventaja y obligación, todo el código de Clean Architecture (`domain/`, `data/` y componentes reutilizables) debe ubicarse fuera de `app/` para que Expo Router no lo interprete como rutas.
* **Verificación:** Comprobar que `app/` contenga solo layouts y pantallas organizadas por rol, verificar que `package.json` no agregue paquetes manuales extra de React Navigation y comprobar en Android que cada rol accede únicamente a sus rutas.
* **Uso de IA:** Se consultó a la IA sobre la estructura de navegación para flujos diferenciados por rol en Expo. Se aceptó usar Expo Router con grupos de rutas por rol y se descartó pasar borradores de actas por parámetros de navegación porque podrían perderse al cerrar la app en recintos sin señal.

### ADR-002: Usar expo-sqlite para base de datos local y persistencia offline

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-05`, `RF-09`, `RF-13`, `RF-15`, `RF-16`, `RNF-01`, `RNF-02`, `RNF-06`, `S-01`, `S-02`, `S-05`.
* **Contexto:** Durante la jornada electoral, muchos veedores en recintos rurales no tienen señal de internet al finalizar el conteo de votos, y en procesos anteriores las actas anotadas en papel o dictadas tarde se perdieron o llegaron con errores. Además, los usuarios reintentaban envíos sin saber si la información se había guardado, generando duplicados o abandonos, y los coordinadores necesitan consultar el avance de mesas y resultados. El proyecto exige React Native, APK para Android, Clean Architecture y datos precargados de provincia/cantón, recintos y cinco organizaciones políticas con sus candidatos a alcaldía y prefectura. Se necesita un almacenamiento estructurado en el dispositivo que conserve información y estados de operación sin depender de conectividad constante.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **`expo-sqlite`** | Permite guardar recintos, mesas, usuarios, actas (alcaldía y prefectura), su estado (`en_proceso`, `guardado`, `fallo`) y una cola de sincronización en tablas SQL, facilitando consultas por recinto/rol y transacciones atómicas en recintos rurales sin señal. | Exige escribir sentencias SQL, migraciones y mapeo manual hacia las entidades de la capa `domain`. |
| **Realm Database** | Permite trabajar con objetos y esquemas tipados en el teléfono sin escribir consultas SQL manuales para operaciones básicas de lectura y escritura. | Adopta un modelo orientado a objetos sobre un dominio electoral naturalmente relacional y jerárquico, haciendo menos directas ciertas agregaciones y sumando una dependencia adicional que configurar y defender. |
| **WatermelonDB** | Está pensada para apps *offline-first* con carga perezosa (*lazy loading*) y modelos reactivos locales. | Introduce modelos, decoradores y una mecánica de sincronización propia que añade complejidad innecesaria para el alcance del escrutinio cantonal o provincial. |

* **Decisión:** Se elige **`expo-sqlite`** con transacciones locales (`withTransactionAsync`) y modo WAL (*Write-Ahead Logging*) para almacenar los datos precargados, las actas de alcaldía y prefectura, su estado de sincronización y la cola de operaciones pendientes.
* **Consecuencias:** Se garantiza persistencia estructurada y consultas rápidas sin conexión, evitando pérdida de actas o registros duplicados. Como desventaja, obliga a mantener scripts de creación/migración de tablas y adaptadores de mapeo dentro de `data/` para no acoplar SQL al dominio.
* **Verificación:** Revisar el esquema SQL y repositorios locales en `data/`, verificar `expo-sqlite` en `package.json` y probar en el teléfono el registro y edición de actas en modo avión comprobando que persisten tras cerrar y reabrir la app.
* **Uso de IA:** Se pidió a la IA comparar opciones de almacenamiento offline. Inicialmente se evaluaron también AsyncStorage y Firebase Cloud Firestore; tras analizarlo con la IA, se descartó AsyncStorage por ser solo clave-valor sin transacciones ni consultas relacionales, y se descartó comparar Firestore aquí porque es una base remota en la nube, dejando a Realm Database y WatermelonDB como alternativas locales reales. [Añade si ajustaste algún detalle de esta consulta]

### ADR-003: Usar Supabase como backend, autenticación, almacenamiento de fotos y base remota

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-01`, `RF-02`, `RF-03`, `RF-04`, `RF-06`, `RF-07`, `RF-08`, `RF-14`, `RF-15`, `S-03`, `S-05`.
* **Contexto:** La organización cuenta con tres niveles jerárquicos donde nadie se auto-registra, cada titular debe cambiar su clave inicial y recuperar su acceso mediante correo verificado sin llamar al coordinador provincial. Asimismo, cada veedor solo debe ver y editar sus mesas, cada coordinador de recinto solo su recinto, y el coordinador provincial debe monitorear el escrutinio por recinto y total junto con las fotos de las actas. Además, crear cuentas desde un flujo estándar de cliente no debe cerrar la sesión activa del coordinador que crea la cuenta.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **Supabase** | Base de datos relacional PostgreSQL ideal para jerarquía territorial, mesas, reemplazos de veedores y cálculos de *exit poll*, junto con Auth por correo, Storage para fotos, Edge Functions para creación jerárquica sin cerrar sesión y políticas RLS por rol. | Exige diseñar cuidadosamente el esquema relacional y las políticas RLS para impedir fugas entre mesas o recintos, y requiere cola local propia para modo sin señal. |
| **Firebase** | Ofrece Auth, Firestore y Cloud Storage con SDK maduro para React Native. | Su modelo NoSQL documental hace que las consultas relacionales y agregaciones de conteo por recinto y total para alcaldía y prefectura sean más complejas. |
| **Appwrite** | Integra autenticación, base de datos y almacenamiento en una plataforma con opción *self-hosted*. | Si se auto-hospeda exige administrar servidor y disponibilidad el día de elecciones, y en React Native cuenta con menor soporte de ejemplos en Expo/Android que Supabase o Firebase. |

* **Decisión:** Se elige **Supabase** (PostgreSQL + Auth + Storage + políticas RLS, apoyado en creación de cuentas vía Edge Function y almacenamiento seguro de sesión) como backend remoto para gestionar usuarios de manera jerárquica, verificación y recuperación de correo, almacenamiento de fotos de actas y consolidación del escrutinio.
* **Consecuencias:** Se obtiene un modelo relacional sólido para consultar avance de mesas y resultados por candidato en tiempo real. Como desventaja y riesgo, si una política RLS queda mal configurada puede bloquear operaciones legítimas (como el reemplazo de veedores o edición por coordinador de recinto) o exponer datos de otro recinto.
* **Verificación:** Revisar el esquema SQL, políticas RLS y repositorios remotos en `data/`, comprobar `@supabase/supabase-js` en `package.json` y probar en el dispositivo que un veedor no puede consultar actas de mesas ajenas.
* **Uso de IA:** Se pidió a la IA comparar entre Supabase, Firebase y Appwrite para el dominio electoral. Se aceptó Supabase por la afinidad relacional de PostgreSQL con recintos, mesas y conteos de *exit poll*, descartando Firestore por su enfoque documental NoSQL.

### ADR-004: Usar Zustand para el estado compartido de sesión, rol y flujo de registro

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-01`, `RF-07`, `RF-08`, `RF-13`, `RF-14`, `RNF-02`, `S-03`.
* **Contexto:** Durante la jornada electoral, la aplicación necesita compartir entre pantallas el usuario autenticado, su rol y jurisdicción, la mesa seleccionada y el estado actual de las operaciones (*en proceso*, *guardado* o *falló*), evitando que los usuarios repitan acciones por falta de feedback o pierdan el contexto al navegar. El código debe respetar Clean Architecture en React Native.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **Zustand** | Permite crear *stores* ligeros por dominio funcional (sesión/rol, selección de mesa/recinto, estado de sincronización) con mínimo código repetitivo y suscripciones selectivas sin *re-renders* masivos. | Agrega una dependencia externa y puede mezclar lógica de negocio en el *store* si no se delega la ejecución a los casos de uso de `domain/`. |
| **React Context + `useReducer`** | No requiere instalar librerías adicionales al usar APIs nativas de React. | Provoca más *re-renders* en árboles grandes cuando cambian estados frecuentes (como progreso de subida/sincronización) y exige más código de acciones y reducers. |
| **Redux Toolkit** | Ofrece flujo predecible con *slices* y herramientas de depuración estructuradas. | Introduce excesivo *boilerplate* (acciones, *slices*, *store* centralizado) para el tamaño de este proyecto, complicando la revisión y defensa oral. |

* **Decisión:** Se elige **Zustand** únicamente para estado global de presentación/sesión (usuario activo, rol, recinto/mesa en curso y estado visual de operaciones), delegando toda regla de negocio a los casos de uso de `domain/`.
* **Consecuencias:** Se simplifica la coordinación entre pantallas por rol y el seguimiento visual de estados sin sobrecargar el código. Como desventaja, se añade una librería al `package.json` y se requiere disciplina para no meter llamadas directas a SQLite o Supabase dentro de los *stores*.
* **Verificación:** Verificar `zustand` en `package.json`, inspeccionar la carpeta de *stores* en `presentation/` comprobando que invocan casos de uso de `domain/` y probar que el cambio de rol o selección de mesa se refleja consistentemente en todas las pantallas.
* **Uso de IA:** Se pidió a la IA comparar Zustand, Context API y Redux Toolkit. Se aceptó Zustand por su simplicidad y bajo *boilerplate* para estado de sesión y UI, descartando usar Zustand como base de datos offline (responsabilidad asignada a `expo-sqlite`).

### ADR-005: Usar React Hook Form para el manejo de formularios de usuarios, territorio y actas

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-02`, `RF-06`, `RF-08`, `RF-09`, `RF-12`, `RNF-04`, `S-02`, `S-03`.
* **Contexto:** La aplicación contiene múltiples formularios, destacando la transcripción y edición de actas para dos dignidades (alcaldía y prefectura), cada una con votos de cinco organizaciones políticas, votos nulos, votos en blanco y total de sufragantes. Muchos veedores tienen poca experiencia con el celular y cometen errores de digitación, por lo que el formulario debe ser fluido, permitir cargar datos previos al corregir un acta y bloquear envíos repetidos mientras se procesa.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **React Hook Form** | Minimiza *re-renders* al escribir en formularios con múltiples campos numéricos, soporta `reset()` para precargar actas al corregirlas y expone `isSubmitting` para evitar doble envío. | Al basarse en referencias/controladores, exige integrar correctamente inputs personalizados de React Native mediante `Controller`. |
| **TanStack Form** | Brinda tipado estricto y suscripción granular por campo para formularios complejos. | Requiere mayor verbosidad para definir cada campo y cuenta con menos ejemplos móviles directos que React Hook Form. |
| **Estado local con `useState` por campo** | No añade dependencias externas y es directo para formularios pequeños de 2 o 3 campos. | En actas con 8 campos numéricos por dignidad más validaciones y modo edición, genera código repetitivo y *re-renders* en cada pulsación. |

* **Decisión:** Se elige **React Hook Form** para gestionar los formularios de creación de usuarios o recintos, reemplazo de veedores y registro o corrección de actas electorales.
* **Consecuencias:** Mejora la fluidez en dispositivos Android de gama media/baja y estandariza el manejo de errores por campo y bloqueo durante el envío. Como desventaja, suma una dependencia externa y obliga a envolver componentes de entrada personalizados con `Controller`.
* **Verificación:** Revisar `react-hook-form` en `package.json`, inspeccionar el formulario de acta en `presentation/` y comprobar en el teléfono que al corregir un acta existente se cargan sus valores previos y el botón de guardar se deshabilita mientras se procesa.
* **Uso de IA:** Se consultó a la IA entre React Hook Form, TanStack Form y Formik. Se aceptó usar React Hook Form por su rendimiento en formularios numéricos y facilidad para precargar valores en correcciones de actas. 

### ADR-006: Usar Zod para validar cédulas, correos y coherencia numérica de actas

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-02`, `RF-10`, `RF-12`, `RNF-02`, `S-02`.
* **Contexto:** En elecciones anteriores se registraron cédulas mal digitadas que impidieron identificar quién reportó ciertas actas y se enviaron actas con cifras imposibles, como un candidato con más votos que el total de sufragantes o sumas inconsistentes. La aplicación debe detectar estos errores en el momento en que se ingresan o corrigen los datos, tanto en pantallas de registro como al validar entidades bajo Clean Architecture.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **Zod** | Permite definir esquemas tipados reutilizables con reglas cruzadas (`superRefine`) para validar dígito verificador de cédula ecuatoriana, formato de correo/teléfono y cuadre entre votos de candidatos, nulos, blancos y total de sufragantes. | Añade una dependencia en `package.json` y requiere sincronizar sus mensajes con los componentes de interfaz. |
| **Yup** | Permite validar esquemas de formularios y condiciones entre campos con API conocida. | Resulta menos natural que Zod para inferir tipos TypeScript estrictos y expresar validaciones cruzadas complejas de escrutinio. |
| **Validaciones manuales con `if/else`** | No agrega librerías externas. | Repetir validaciones en creación de usuarios, actas de alcaldía, actas de prefectura y correcciones aumenta el riesgo de omitir reglas en alguno de los flujos. |

* **Decisión:** Se elige **Zod** para centralizar los esquemas de validación de usuarios y actas electorales, ejecutándose antes de guardar o enviar cualquier registro.
* **Consecuencias:** Se bloquean cédulas inválidas y actas matemáticamente imposibles en tiempo real, reutilizando las mismas reglas al crear y al corregir actas. Como desventaja, se incorpora una librería externa y se exige mantener los esquemas desacoplados para no mezclar UI con reglas de validación.
* **Verificación:** Revisar los esquemas de validación en `domain/`, verificar `zod` en `package.json` y probar en la app ingresando una cédula incorrecta o un candidato con más votos que el total de sufragantes comprobando que se impide guardar.
* **Uso de IA:** Se pidió a la IA comparar Zod, Yup y validación imperativa. Se aceptó Zod con `superRefine` para reglas cruzadas de votos vs. sufragantes y se descartó validar únicamente en el backend porque en recintos rurales sin conexión el error debe detectarse localmente al instante. 

### ADR-007: Usar expo-camera para capturar y verificar la fotografía de respaldo del acta

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-11`, `RF-16`, `RNF-04`, `S-05`.
* **Contexto:** Cada acta de alcaldía y prefectura debe respaldarse con una fotografía. En el proceso anterior, casi la mitad de las fotos recibidas fueron borrosas o ilegibles y no sirvieron para impugnar resultados. La aplicación debe permitir capturar la foto del acta en Android, mostrar una vista previa clara (con zoom/revisión de nitidez) para confirmar legibilidad antes de asociarla al acta y conservar el archivo localmente comprimido si no hay internet.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **`expo-camera`** | Permite integrar una vista de captura controlada dentro de la app con guía de encuadre y previsualización obligatoria para revisar nitidez antes de guardar el acta, combinable con compresión local para redes lentas. | Requiere gestionar permisos de cámara, ciclo de vida del componente de cámara y almacenamiento local de la imagen. |
| **`expo-image-picker`** | Implementación más rápida delegando la captura a la aplicación de cámara del sistema Android. | Ofrece menor control sobre la experiencia guiada de encuadre y facilita seleccionar imágenes antiguas de galería si no se restringe adecuadamente. |

* **Decisión:** Se elige **`expo-camera`** junto con almacenamiento en sistema de archivos local (`expo-file-system`), compresión previa de imagen para envíos ágiles y pantalla de previsualización/confirmación obligatoria antes de guardar el acta.
* **Consecuencias:** Se mejora la calidad y trazabilidad visual de las actas capturadas en mesa y se evita guardar fotos en base de datos como texto Base64. Como desventaja, requiere manejar permisos denegados de cámara y espacio de almacenamiento local en el dispositivo.
* **Verificación:** Verificar `expo-camera` en `package.json`, revisar el componente de captura/previsualización en `presentation/` y el adaptador de archivos en `data/`, y probar en Android la toma, rechazo (por borrosa) y confirmación de foto de un acta.
* **Uso de IA:** Se consultó a la IA entre `expo-camera` y `expo-image-picker`. Se eligió `expo-camera` para forzar captura en vivo con revisión de legibilidad y se descartó guardar la foto en Base64 dentro de SQLite para no degradar el rendimiento de la base local. 

### ADR-008: Usar expo-location para registrar la geolocalización de cada acta

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-11`, `RF-15`, `RNF-06`, `S-04`.
* **Contexto:** En elecciones anteriores la organización no pudo demostrar que ciertas fotos y actas se registraron en el recinto electoral, recibiendo acusaciones de que fueron tomadas en otro lugar. Cada acta registrada debe guardar evidencia de ubicación geográfica consultable posteriormente por los coordinadores (incluyendo su visualización en mapa al revisar el acta), incluso si en el momento del conteo el teléfono no tiene datos móviles.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **`expo-location`** | Obtiene latitud, longitud y precisión directamente del GPS/servicios de ubicación de Android, funcionando también sin datos móviles (vía satélite GPS) y guardando coordenadas junto al acta para su revisión en mapa. | Depende de permisos de ubicación del usuario y puede tardar o perder precisión en aulas cerradas sin vista al cielo. |
| **Ingreso manual de ubicación por el veedor** | No depende de permisos GPS ni hardware de ubicación. | No aporta prueba objetiva de dónde se tomó el acta y permite falsificar o equivocar el lugar de registro. |

* **Decisión:** Se elige **`expo-location`** para capturar automáticamente coordenadas (`latitud`, `longitud`, `precisión` y marca de tiempo) al confirmar el acta, almacenándolas junto al registro y mostrándolas (junto con su vista en mapa) en el detalle de revisión del acta.
* **Consecuencias:** Cada acta queda respaldada con ubicación verificable consultable por el coordinador. Como desventaja y riesgo, si el GPS tarda en interiores o el permiso es denegado, la app debe manejar un *timeout* claro y registrar el estado de ubicación según el supuesto `S-04` sin bloquear indefinidamente la interfaz.
* **Verificación:** Revisar `expo-location` en `package.json`, inspeccionar el servicio de ubicación en `data/` y verificar en la pantalla de detalle de un acta que se muestran las coordenadas/ubicación desde donde fue registrada.
* **Uso de IA:** Se consultó a la IA cómo garantizar trazabilidad geográfica de las actas. Se aceptó `expo-location` con captura de coordenadas y precisión al confirmar el acta, descartando depender solo de geocodificación inversa en línea porque puede no haber internet en recintos rurales.
### ADR-009: Usar NetInfo con cola local en SQLite para sincronización automática al recuperar conexión

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-13`, `RF-15`, `RF-16`, `RNF-06`, `S-05`.
* **Contexto:** Muchos veedores en recintos rurales no tienen señal al finalizar el conteo, y en procesos anteriores las actas se perdieron o los usuarios reintentaron envíos sin saber su estado, duplicando registros. La aplicación debe detectar cambios de conectividad, guardar localmente cuando no hay red y sincronizar automáticamente actas, fotos y reportes pendientes cuando vuelva la señal, mostrando siempre el estado exacto.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **`@react-native-community/netinfo` + cola en `expo-sqlite`** | Detecta en tiempo real la pérdida y recuperación de red para disparar el procesamiento ordenado de actas y fotos pendientes usando un identificador único idempotente que evita duplicados. | Añade la dependencia de `netinfo` y exige programar la máquina de estados (`pendiente`, `en_proceso`, `guardado`, `fallo`) y reintentos. |
| **Sincronización únicamente manual con botón "Reintentar"** | Evita detectar cambios de red en segundo plano y simplifica la lógica de suscripción. | Depende de que veedores con poca experiencia recuerden abrir la app y presionar reintentar horas después al salir del recinto rural. |

* **Decisión:** Se elige **`@react-native-community/netinfo`** combinado con una cola transaccional en **`expo-sqlite`** e identificadores UUID idempotentes por acta/operación para sincronizar automáticamente al detectar conexión y reflejar el estado en tiempo real.
* **Consecuencias:** Garantiza que ningún acta se pierda por falta de señal y elimina duplicados ante reintentos. Como desventaja, requiere controlar bloqueos de concurrencia para que dos disparos simultáneos de red no procesen el mismo elemento de la cola a la vez.
* **Verificación:** Verificar `@react-native-community/netinfo` en `package.json`, revisar el sincronizador en `data/` y probar en Android registrando un acta en modo avión (estado *pendiente/guardado local*) y activando datos/Wi-Fi para ver su paso automático a *sincronizado*.
* **Uso de IA:** Se pidió a la IA diseñar el flujo *offline-first* para recintos sin señal. Se aceptó combinar NetInfo + cola SQLite + UUID idempotentes y se descartó confiar únicamente en reintentos en memoria porque se perderían si el usuario cierra la aplicación.

### ADR-010: Usar Sentry para detección y reporte de fallos con cola offline

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-17`, `RNF-06`, `S-05`, `S-06`.
* **Contexto:** En elecciones pasadas una app se cerraba sola en ciertos teléfonos y los veedores enviaban mensajes vagos por WhatsApp sin que el equipo técnico conociera la pantalla, el dispositivo ni la versión de Android. Además, se exige capturar errores no fatales (como fallos al subir fotos), conservar reportes sin conexión para enviarlos cuando vuelva la red.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **Sentry (`@sentry/react-native`)** | Captura *crashes* nativos/JS y errores capturados manualmente (`captureException`), adjunta dispositivo, versión de Android y ruta/pantalla actual, almacena eventos en caché local sin internet y permite verificarlos en consola durante la defensa. | Agrega una dependencia nativa/JS que requiere configuración de *DSN* y filtrado para no saturar eventos con validaciones normales de usuario. |
| **Firebase Crashlytics** | Buena captura de cierres inesperados en Android con contexto de dispositivo. | Si el backend principal es Supabase, introduce un segundo ecosistema cloud adicional solo para telemetría y resulta menos directo para agrupar errores funcionales personalizados de subida. |
| **Tabla de logs local propia sin plataforma externa** | No requiere SDKs de terceros. | Carece de agrupación automática de incidentes, conteo de usuarios afectados y panel centralizado listo para inspección inmediata. |

* **Decisión:** Se elige **Sentry (`@sentry/react-native`)** configurado con contexto de rol/pantalla y captura explícita de errores críticos (fallos de sincronización, subida de fotos y excepciones no controladas).
* **Consecuencias:** Permite diagnosticar fallos reales con modelo de celular, versión de Android, pantalla y cantidad de usuarios afectados sin depender de reportes por WhatsApp. Como desventaja, suma configuración nativa para compilar el APK de Android y obliga a cuidar no enviar datos sensibles innecesarios (como contraseñas) en los metadatos del error.
* **Verificación:** Verificar `@sentry/react-native` en `package.json`, revisar la inicialización y llamadas `captureException` en `data/` y provocar un fallo controlado en la app (con y sin conexión) comprobando su recepción en el dashboard de Sentry.
* **Uso de IA:** Se consultó a la IA entre Sentry, Crashlytics y logs propios. Se aceptó Sentry porque cubre tanto *crashes* como errores no fatales de flujo (subida de foto) con envío diferido offline . 

### ADR-011: Usar React Native Reanimated y Lottie para retroalimentación visual y estados de operación

* **Estado:** Aceptado
* **Fecha:** 2026-11-21
* **Requisitos y supuestos relacionados:** `RF-13`, `RNF-04`, `RNF-05`, `S-01`.
* **Contexto:** Los usuarios necesitan saber en todo momento si una operación está en proceso, se guardó o falló para evitar reintentos duplicados o abandono. Asimismo, la rúbrica exige explícitamente el uso de **Lottie** y **Reanimated** con mensajes claros sin modales genéricos del sistema, penalizando aplicaciones visualmente genéricas.
* **Alternativas consideradas:**

| Alternativa | Ventajas en este proyecto | Desventajas en este proyecto |
| :--- | :--- | :--- |
| **`react-native-reanimated` + `lottie-react-native`** | Cumple el requisito técnico de la rúbrica, ejecuta transiciones fluidas en el hilo nativo y comunica visualmente estados de carga, éxito, error y validación sin bloquear con alertas genéricas. | Añade dos dependencias con componentes nativos que deben compilarse correctamente al generar el APK de Android y requiere recursos `.json` de Lottie livianos. |
| **API `Animated` nativa de React Native** | Viene integrada en React Native sin dependencias adicionales. | No soporta animaciones vectoriales Lottie y exige más código imperativo para transiciones complejas y feedback rico. |

* **Decisión:** Se eligen **`react-native-reanimated`** y **`lottie-react-native`** integrados en componentes visuales propios de retroalimentación (*banners/estados en vivo*, indicadores de progreso, confirmación de guardado y alertas de validación con los colores de la organización política).
* **Consecuencias:** Se logra una interfaz clara, accesible y diferenciada visualmente que evita duplicados por incertidumbre del usuario. Como desventaja, incrementa ligeramente el tamaño del APK y requiere probar las animaciones en el APK compilado (no solo en desarrollo).
* **Verificación:** Verificar `react-native-reanimated` y `lottie-react-native` en `package.json`, comprobar que no se usen `Alert.alert` genéricos para operaciones principales y observar las animaciones de estado (*en proceso*, *guardado*, *falló*) en el APK instalado en Android.
* **Uso de IA:** Se pidió a la IA sugerencias de patrones de feedback visual para usuarios mayores o con poca experiencia. Se aceptó combinar indicadores animados con Lottie/Reanimated y banners persistentes de estado, descartando modales genéricos del sistema porque interrumpen el flujo. 