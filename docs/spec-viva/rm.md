## Purpose

Describe el comportamiento vigente de cuentas y acceso de FlowSync: registro, inicio y cierre de sesión, consulta del perfil, protección de rutas y pantallas relacionadas. Sirve como referencia viva de lo que hoy observan tanto quienes usan la aplicación web como quienes consumen su API.

## Requirements

### Requirement: Registro de una cuenta nueva

El sistema SHALL permitir crear una cuenta mediante `POST /api/v1/auth/signup` con email, contraseña, confirmación de contraseña y nombre completo, que puede ser nulo. Al crearla, SHALL abrir una sesión y devolver dentro de `data` los datos públicos del usuario y una credencial de acceso.

#### Scenario: Registro correcto con nombre

- **WHEN** se envía un registro con nombre completo, un email válido no registrado y una contraseña válida correctamente confirmada
- **THEN** la API responde 200 con el identificador, el nombre completo, el email, las iniciales y las fechas de creación y actualización del usuario, junto con una credencial de acceso

#### Scenario: Registro correcto con nombre nulo

- **WHEN** se envía un registro válido con el nombre completo a nulo
- **THEN** la API responde 200, el nombre completo del usuario queda nulo y sus iniciales se forman con la primera letra del email y la primera letra de su dominio

#### Scenario: Nombre completo ausente

- **WHEN** se envía a la API un registro sin incluir el campo de nombre completo
- **THEN** la API responde 422 con un error de campo obligatorio sobre el nombre completo

### Requirement: Validación de los datos de registro

El sistema SHALL rechazar con un 422 y errores por campo cualquier registro cuyo email no sea válido o supere los 254 caracteres, cuyo email ya exista, cuya contraseña no tenga entre 8 y 32 caracteres o cuya confirmación no coincida con la contraseña. La unicidad del email SHALL distinguir entre mayúsculas y minúsculas.

#### Scenario: Email con formato no válido

- **WHEN** se envía un registro con un email sin formato de dirección válida
- **THEN** la API responde 422 con un error sobre el email y no crea la cuenta

#### Scenario: Email ya registrado

- **WHEN** se envía un registro con un email idéntico al de una cuenta existente
- **THEN** la API responde 422 con un error de unicidad sobre el email y no crea la cuenta

#### Scenario: Mismo email con distinta capitalización

- **WHEN** se envía un registro válido con un email que solo difiere de uno existente en mayúsculas y minúsculas
- **THEN** la API responde 200 y crea una cuenta independiente

#### Scenario: Contraseña demasiado corta

- **WHEN** se envía un registro con una contraseña y una confirmación de menos de 8 caracteres
- **THEN** la API responde 422 con errores de longitud mínima sobre ambos campos

#### Scenario: Contraseña demasiado larga

- **WHEN** se envía un registro con una contraseña y una confirmación de más de 32 caracteres
- **THEN** la API responde 422 con errores de longitud máxima sobre ambos campos

#### Scenario: Confirmación distinta en la API

- **WHEN** se envía a la API un registro cuya confirmación no coincide con la contraseña
- **THEN** la API responde 422 con un error sobre la confirmación y no crea la cuenta

### Requirement: Pantalla de registro

La aplicación web SHALL ofrecer en `/register` un formulario con nombre completo opcional, email, contraseña y repetición de la contraseña, junto con un enlace a `/login`. SHALL mostrar los errores en castellano y dejar la sesión iniciada cuando el registro tenga éxito.

#### Scenario: Registro completado desde la pantalla

- **WHEN** una persona anónima envía el formulario con datos válidos
- **THEN** la sesión queda iniciada y la aplicación la lleva a `/profile`

#### Scenario: Nombre completo en blanco

- **WHEN** una persona envía el formulario con el nombre completo vacío o formado solo por espacios
- **THEN** la cuenta se crea sin nombre completo

#### Scenario: Contraseñas distintas en la pantalla

- **WHEN** una persona envía el formulario con dos contraseñas diferentes
- **THEN** la pantalla muestra «Las contraseñas no coinciden.» bajo la repetición de la contraseña sin enviar la petición al servidor

#### Scenario: Email ya registrado en la pantalla

- **WHEN** el servidor rechaza el registro porque el email ya existe
- **THEN** la pantalla muestra bajo el email «Ese email ya está registrado. Inicia sesión en su lugar.»

#### Scenario: Envío en curso

- **WHEN** el registro se está enviando
- **THEN** el botón aparece deshabilitado con el texto «Creando cuenta…»

### Requirement: Inicio de sesión

El sistema SHALL permitir iniciar sesión mediante `POST /api/v1/auth/login` con email y contraseña, y SHALL devolver dentro de `data` los datos públicos del usuario y una credencial de acceso nueva. El rechazo por credenciales incorrectas SHALL ser idéntico tanto si el email existe como si no.

#### Scenario: Credenciales correctas

- **WHEN** se envían el email y la contraseña correctos de una cuenta
- **THEN** la API responde 200 con los datos públicos del usuario y una credencial de acceso

#### Scenario: Contraseña incorrecta

- **WHEN** se envía un email registrado con una contraseña incorrecta
- **THEN** la API responde 400 con un error general de credenciales no válidas

#### Scenario: Email desconocido

- **WHEN** se envía un email con formato válido que no pertenece a ninguna cuenta
- **THEN** la API responde 400 con el mismo error general de credenciales no válidas

#### Scenario: Campos vacíos

- **WHEN** se envían el email y la contraseña como cadenas vacías
- **THEN** la API responde 422 con errores de campo obligatorio sobre ambos

#### Scenario: Email con formato no válido al iniciar sesión

- **WHEN** se envía un email sin formato de dirección válida
- **THEN** la API responde 422 con un error sobre el email

### Requirement: Pantalla de inicio de sesión

La aplicación web SHALL ofrecer en `/login` un formulario de email y contraseña, junto con un enlace a `/register`, y SHALL mostrar los errores en castellano.

#### Scenario: Acceso correcto desde la pantalla

- **WHEN** una persona anónima envía credenciales correctas
- **THEN** la sesión queda iniciada y la aplicación la lleva a `/profile`

#### Scenario: Credenciales rechazadas en la pantalla

- **WHEN** el servidor rechaza las credenciales
- **THEN** la pantalla muestra el aviso «El email o la contraseña no son correctos.»

#### Scenario: Campos vacíos en la pantalla

- **WHEN** una persona envía el formulario con el email vacío
- **THEN** la pantalla muestra bajo el email «Falta rellenar el email.»

#### Scenario: Error del servidor en la pantalla

- **WHEN** el servidor responde con un error interno al enviar el formulario
- **THEN** la pantalla muestra el aviso «Algo ha ido mal en el servidor. Inténtalo de nuevo en un momento.»

#### Scenario: Servidor inaccesible

- **WHEN** el formulario se envía y no se puede conectar con el servidor
- **THEN** la pantalla muestra el aviso «No se pudo conectar con el servidor. Comprueba que el backend está arrancado.»

#### Scenario: Envío de acceso en curso

- **WHEN** el inicio de sesión se está enviando
- **THEN** el botón aparece deshabilitado con el texto «Entrando…»

### Requirement: Continuidad de la sesión en el navegador

La aplicación web SHALL conservar la sesión entre recargas y, al arrancar con una sesión guardada, SHALL comprobarla contra el servidor antes de darla por válida. La aplicación SHALL NOT imponer una caducidad propia a la sesión.

#### Scenario: Recarga con sesión válida

- **WHEN** se recarga la aplicación y el servidor reconoce la sesión guardada
- **THEN** la sesión continúa activa y la persona no vuelve a introducir sus credenciales

#### Scenario: Sesión rechazada por el servidor

- **WHEN** se abre `/login` con una sesión guardada y el servidor responde 401 a esa sesión
- **THEN** la sesión guardada se elimina y `/login` muestra «Tu sesión ha caducado. Vuelve a iniciar sesión.»

#### Scenario: Servidor no disponible al restaurar

- **WHEN** se abre `/login` y la sesión guardada no se puede comprobar por un fallo de conexión o del servidor
- **THEN** `/login` muestra el motivo del fallo y la sesión guardada se conserva para volver a comprobarla en la siguiente carga

#### Scenario: Comprobación en curso

- **WHEN** se abre `/profile`, `/login` o `/register` mientras se comprueba la sesión guardada
- **THEN** se muestra un indicador de carga a pantalla completa sin redirigir

### Requirement: Cierre de sesión

El sistema SHALL permitir cerrar sesión mediante `POST /api/v1/account/logout` con una credencial válida, y SHALL invalidar únicamente la credencial usada en esa petición.

#### Scenario: Cierre correcto en la API

- **WHEN** se solicita el cierre de sesión con una credencial válida
- **THEN** la API responde 200 con un mensaje de confirmación y esa credencial recibe 401 en peticiones posteriores

#### Scenario: Otras sesiones de la misma cuenta

- **WHEN** una cuenta tiene dos sesiones abiertas y cierra una de ellas
- **THEN** la otra credencial sigue siendo válida

#### Scenario: Cierre sin credencial válida

- **WHEN** se solicita el cierre de sesión sin credencial o con una ya invalidada
- **THEN** la API responde 401

### Requirement: Cierre de sesión desde la aplicación web

La aplicación web SHALL ofrecer en la pantalla de perfil un botón «Cerrar sesión» que SHALL finalizar la sesión en el navegador aunque falle la petición al servidor.

#### Scenario: Cierre desde el perfil

- **WHEN** una persona autenticada pulsa «Cerrar sesión»
- **THEN** la sesión guardada se elimina y la aplicación la lleva a `/login` sin mostrar ningún aviso de error

#### Scenario: Fallo del servidor durante el cierre

- **WHEN** una persona pulsa «Cerrar sesión» y la petición al servidor falla
- **THEN** la sesión termina igualmente en el navegador y la aplicación la lleva a `/login` sin mostrar el fallo

### Requirement: Consulta del perfil

El sistema SHALL devolver, mediante `GET /api/v1/account/profile` y dentro de `data`, los datos públicos del usuario autenticado: identificador, nombre completo, email, iniciales y fechas de creación y actualización. La respuesta SHALL NOT incluir la contraseña.

#### Scenario: Perfil con credencial válida

- **WHEN** se consulta el perfil con una credencial válida
- **THEN** la API responde 200 con los datos públicos de la cuenta a la que pertenece la credencial

### Requirement: Pantalla de perfil

La aplicación web SHALL mostrar en `/profile` las iniciales, el nombre completo, el email y la fecha de alta de la persona autenticada.

#### Scenario: Persona con nombre completo

- **WHEN** una persona autenticada con nombre completo abre `/profile`
- **THEN** la pantalla muestra sus iniciales, su nombre, su email y «Miembro desde» con la fecha larga en castellano

#### Scenario: Persona sin nombre completo

- **WHEN** una persona autenticada sin nombre completo abre `/profile`
- **THEN** la pantalla muestra «Sin nombre» en lugar del nombre, junto con sus iniciales y su email

### Requirement: Protección de la API

El sistema SHALL exigir una credencial válida para consultar el perfil y cerrar sesión. El registro y el inicio de sesión SHALL permanecer accesibles sin credencial.

#### Scenario: Petición sin credencial

- **WHEN** se consulta el perfil sin enviar credencial
- **THEN** la API responde 401 con un error de acceso no autorizado

#### Scenario: Credencial no válida

- **WHEN** se consulta el perfil con una credencial inexistente o mal formada
- **THEN** la API responde 401 con un error de acceso no autorizado

### Requirement: Protección de rutas en la aplicación web

La aplicación web SHALL restringir `/profile` a personas autenticadas y `/login` y `/register` a personas anónimas. Cualquier otra ruta SHALL redirigir a `/profile`, donde se aplica la misma protección.

#### Scenario: Ruta protegida sin sesión

- **WHEN** una persona anónima abre `/profile`
- **THEN** la aplicación la redirige a `/login`

#### Scenario: Pantallas de acceso con sesión activa

- **WHEN** una persona autenticada abre `/login` o `/register`
- **THEN** la aplicación la redirige a `/profile`

#### Scenario: Ruta desconocida sin sesión

- **WHEN** una persona anónima abre una ruta no definida
- **THEN** la aplicación la lleva a `/login`

#### Scenario: Ruta desconocida con sesión

- **WHEN** una persona autenticada abre una ruta no definida
- **THEN** la aplicación la lleva a `/profile`

## Parte B: comprobación

### 1. Requisitos escritos y comprobados

- Requisitos escritos por el agente: 12
- Requisitos comprobados por mí: 12
- Escenarios revisados: 43

### 2. Incoherencias encontradas

- Cuando el servidor no está disponible al restaurar una sesión, el token se conserva en `localStorage`, pero la aplicación pasa temporalmente a estado anónimo y muestra el login. La spec debe distinguir entre conservar el token y mantener la sesión activa.
- La pantalla de cierre de sesión deshabilita el botón y muestra «Cerrando sesión…», pero ese comportamiento no está descrito en la spec.

### 3. Comportamientos que no sé si son bug o contrato

- Los emails que solo cambian en mayúsculas y minúsculas pueden representar cuentas distintas por decisión del contrato, o puede tratarse de un bug si el producto considera que los emails no distinguen mayúsculas.
- Las sesiones sin caducidad configurada pueden ser el contrato actual de sesiones persistentes, o un riesgo de seguridad que debería corregirse.
- Si el servidor no responde al restaurar la sesión, el token se conserva, pero la persona queda fuera de las rutas protegidas hasta recargar. Puede ser una decisión correcta para no bloquear la aplicación, o una experiencia de sesión inconsistente.
