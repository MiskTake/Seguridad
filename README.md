# 🔐 Seguridad — Actividad Grupal

Guía de conceptos básicos de seguridad informática y su impacto directo en el desarrollo de aplicaciones: backend, frontend y APIs.

> *"Un sistema no falla solo por bugs… muchas veces falla porque alguien lo atacó. Como developers, no basta con que funcione… debe ser seguro."*

---

## 🎯 Objetivo

- Comprender los conceptos básicos de seguridad informática
- Entender cómo impactan directamente en el desarrollo
- Identificar vulnerabilidades comunes y cómo evitarlas

---

## 1. Conceptos base

| Concepto | Definición simple | Nivel técnico breve | Ejemplo real |
|---|---|---|---|
| **Permissions** | Sistema que define qué puede hacer cada usuario dentro de una app. | Control de acceso basado en roles. Define qué recursos puede leer, escribir o ejecutar cada usuario. | Un usuario ve su perfil. Un admin puede ver y editar todos los perfiles. |
| **Password segura** | Contraseña difícil de adivinar o romper por un atacante. | Mínimo 12 caracteres, mayúsculas, números y símbolos. Nunca guardada en texto plano. | `Xq7#mL2!pRvN` es fuerte. `P@ssw0rd123` es débil por ser predecible. |
| **Vulnerabilidad** | Falla en el código o configuración que un atacante puede explotar. | Punto débil que permite acceso no autorizado, robo de datos o alteración del sistema. | Campo de formulario sin validación permite inyectar código malicioso. |
| **Intrusión** | Acceso no autorizado a un sistema, red o aplicación. | Ataque donde alguien explota una vulnerabilidad para entrar sin permiso. | Atacante usa SQL Injection para acceder a la BD y robar usuarios. |
| **Seguridad de red** | Medidas que protegen el tráfico y los sistemas dentro de una red. | Uso de firewalls, cifrado HTTPS/TLS, autenticación y monitoreo para prevenir accesos no autorizados. | API que solo acepta HTTPS y bloquea IPs sospechosas con firewall. |

---

## 2. Permissions — accesos en sistemas

### Autenticación vs Autorización

- **Autenticación:** verificar quién eres. Confirma la identidad del usuario, generalmente con usuario y contraseña o un token.
- **Autorización:** verificar qué puedes hacer. Una vez autenticado, el sistema decide a qué recursos tienes acceso.

> La diferencia clave: puedes estar autenticado (el sistema sabe quién eres) pero no autorizado (no tienes permiso para hacer lo que intentas). Son dos verificaciones distintas y ambas son obligatorias.

### Ejemplo developer

- **Login:** es autenticación. El usuario ingresa credenciales y el sistema confirma su identidad.
- **Roles (admin/user):** es autorización. Un usuario con rol `user` no puede acceder a rutas de administración aunque esté logueado.

En APIs: cada endpoint debe verificar primero si el usuario está autenticado (¿tiene token válido?) y luego si tiene permiso para esa acción (¿su rol permite esto?).

---

## 3. Passwords seguras

### ¿Qué hace segura una contraseña?
- Mínimo 12 caracteres
- Combinación de mayúsculas, minúsculas, números y símbolos
- Sin palabras del diccionario ni datos personales
- Única por servicio — nunca reutilizar

### ¿Por qué no guardar contraseñas en texto plano?
Si la base de datos es comprometida, el atacante obtiene todas las contraseñas directamente. Un dump de la BD se convierte en un desastre total para todos los usuarios.

### ¿Qué es hashing?
Función que transforma la contraseña en una cadena irreversible. No se puede deshacer para obtener la contraseña original. Al hacer login, se hashea el input y se compara con el hash guardado.

### ¿Cómo se manejan contraseñas en backend?

```
1. Usuario registra contraseña 'mipass123'
2. Backend aplica bcrypt: '$2b$10$abc...xyz'
3. Se guarda el hash en la BD, nunca la contraseña
4. Al hacer login: bcrypt.compare('mipass123', hash) → true/false

Nunca se guarda ni se transmite la contraseña original.
```

---

## 4. Vulnerabilidades

Una vulnerabilidad es una falla en el código, configuración o diseño que permite que alguien haga algo que no debería poder hacer.

### SQL Injection
El atacante inserta código SQL malicioso en un campo de input. Si el backend no valida, ese código se ejecuta directamente en la base de datos.

> Input del atacante: `' OR '1'='1`
> Consulta resultante: `SELECT * FROM usuarios WHERE email = '' OR '1'='1'`
> Resultado: devuelve TODOS los usuarios de la base de datos.

### XSS — Cross-Site Scripting
El atacante inyecta código JavaScript en la app que luego se ejecuta en el navegador de otros usuarios. Puede robar cookies, tokens de sesión o redirigir a sitios falsos.

### Exposición de datos
Información sensible expuesta accidentalmente: en logs, respuestas de API, variables de entorno públicas o repositorios de código. Un archivo `.env` subido a GitHub expone claves de base de datos y APIs a todo el mundo.

---

## 5. Intrusión

### ¿Qué es una intrusión?
Es cuando un atacante accede a un sistema sin autorización, generalmente explotando una vulnerabilidad.

### ¿Qué busca un atacante?
- Datos personales para vender: emails, contraseñas, tarjetas de crédito
- Acceso a sistemas internos para moverse lateralmente a otros servicios
- Control del servidor para usarlo en otros ataques
- Dinero directo mediante ransomware o fraude

### ¿Qué pasa si una app es comprometida?
- Robo de datos de usuarios con responsabilidad legal para la empresa
- Acceso a servicios internos: base de datos, APIs privadas, otros servidores
- Reputación dañada y pérdida de confianza de usuarios y clientes
- Costos económicos: recuperación, multas, tiempo de ingeniería

### Ejemplo real
Un atacante encuentra una ruta de API sin autenticación. Descarga toda la base de datos de usuarios, intenta crackear los hashes con diccionarios y reutiliza las contraseñas que funcionan en otros servicios populares.

---

## 6. Seguridad de red

### ¿Qué protege?
El tráfico entre clientes y servidores, el acceso a los servidores desde internet y la comunicación interna entre servicios.

### ¿Qué es un firewall?
Sistema que filtra el tráfico de red según reglas. Bloquea conexiones no autorizadas y permite solo el tráfico esperado. En cloud, se configura qué puertos y qué IPs pueden conectarse a cada servicio.

### ¿Qué es HTTPS y por qué importa?
HTTPS es HTTP con cifrado TLS. Todo el tráfico viaja encriptado. Sin HTTPS, cualquiera en la misma red puede leer el tráfico (ataque man-in-the-middle). Con HTTPS, aunque intercepten el tráfico, no pueden leerlo.

### ¿Por qué usar HTTPS en APIs?
Porque si el frontend envía tokens, contraseñas o datos sensibles sin cifrado, cualquiera en la red puede interceptarlos. En producción, toda API debe operar sobre HTTPS obligatoriamente.

---

## 7. Conexión directa con desarrollo

| Error | Consecuencia |
|---|---|
| No validar inputs | SQL Injection, XSS, comportamientos inesperados en la app. |
| No proteger rutas | Cualquier usuario accede a datos o acciones que no le corresponden. |
| Guardar contraseñas en texto plano | Robo masivo de credenciales si la base de datos es comprometida. |
| Subir `.env` a GitHub | Exposición pública de claves de API, base de datos y servicios. |
| No usar HTTPS | Tráfico interceptable, tokens y contraseñas robados en tránsito. |
| No limitar intentos de login | Ataques de fuerza bruta exitosos para adivinar contraseñas. |

### ¿Qué pasaría si...?
- **No validas inputs:** un atacante inyecta SQL y vacía tu base de datos.
- **No proteges rutas:** un usuario normal accede a rutas de admin y lee o modifica datos de todos.
- **No usas HTTPS:** un atacante en la misma red WiFi intercepta el token de sesión de un usuario y se hace pasar por él.

---

## 8. Caso práctico — usuario accede a información que no le corresponde

### ¿Qué falló?
Falla de autorización. El sistema verificó que el usuario estaba autenticado (login correcto) pero no verificó si tenía permiso para acceder a ese recurso específico.

### ¿Es permiso, vulnerabilidad o intrusión?
Es una vulnerabilidad de control de acceso: el código no valida que el recurso solicitado pertenece al usuario que lo pide. El usuario logueado con id `456` solicita datos del usuario `123` y el backend los entrega sin verificar.

### ¿Cómo lo evitarías?
- Validar siempre en el backend que el recurso solicitado pertenece al usuario autenticado
- Nunca confiar solo en el frontend para controlar accesos
- Implementar middleware de autorización en cada ruta sensible
- Hacer pruebas de acceso cruzado entre usuarios antes de deployar

---

## 9. Analogías

| Concepto | Analogía |
|---|---|
| **Permissions** | Niveles de acceso en un edificio: el empleado entra a su oficina, el gerente a todas, el guardia solo a seguridad. |
| **Password** | La llave de tu casa: si es fácil de copiar o la dejas bajo el felpudo, cualquiera puede entrar. |
| **Vulnerabilidad** | Una puerta mal cerrada: nadie la rompió, simplemente no estaba bien asegurada. |
| **Intrusión** | Un ladrón que encontró la puerta abierta y entró — no importa si fue descuido o ataque deliberado. |
| **Seguridad de red** | El sistema de seguridad del edificio completo: cámaras, guardias, alarmas y puertas con llave. |

---

## 10. Bonus — JWT, Tokens y Rate Limiting

### ¿Qué es JWT?
JSON Web Token. Estándar para transmitir información de autenticación de forma segura entre cliente y servidor. El servidor genera un token firmado al hacer login; el cliente lo guarda y lo envía en cada petición. El servidor verifica la firma sin necesidad de consultar la base de datos en cada request.

### ¿Qué es autenticación con tokens?
En vez de guardar sesión en el servidor, se emite un token al cliente tras el login. El cliente lo incluye en el header de cada petición (`Authorization: Bearer <token>`). El servidor verifica el token y sabe quién es el usuario sin mantener estado.

### ¿Qué es rate limiting?
Limitar la cantidad de peticiones que un cliente puede hacer en un período de tiempo. Previene ataques de fuerza bruta (intentar miles de contraseñas) y protege el servidor de sobrecarga. Por ejemplo: máximo 5 intentos de login por minuto por IP — si se supera, se bloquea por 15 minutos.

---

## 11. Glosario de términos

| Término | ¿Qué es? (en simple) | Ejemplo de uso |
|---|---|---|
| **Autenticación** | Verificar quién eres. Confirmar la identidad del usuario. | El login verifica que el email y contraseña coinciden con los de la BD. |
| **Autorización** | Verificar qué puedes hacer. Controlar el acceso según el rol. | Un usuario con rol `user` no puede acceder a rutas de administración. |
| **Hashing** | Transformar datos en una cadena irreversible. No se puede deshacer. | bcrypt convierte `mipass123` en `$2b$10$abc...`. Se guarda el hash, no la contraseña. |
| **bcrypt** | Algoritmo de hashing diseñado para contraseñas. Lento a propósito para dificultar ataques. | `bcrypt.hash('mipass123', 10)` genera el hash. `bcrypt.compare()` lo verifica al login. |
| **SQL Injection** | Ataque que inserta SQL malicioso en inputs sin validar para manipular la BD. | Input `' OR '1'='1` en un login puede devolver todos los usuarios de la BD. |
| **XSS** | Ataque que inyecta JavaScript malicioso para ejecutarse en navegadores de otros usuarios. | Un comentario con script robando cookies se ejecuta cuando otro usuario ve la página. |
| **Vulnerabilidad** | Falla en el código o configuración que un atacante puede explotar. | No validar el input de un formulario es una vulnerabilidad a SQL Injection. |
| **Intrusión** | Acceso no autorizado a un sistema explotando una vulnerabilidad. | Atacante accede a la BD usando SQL Injection en un formulario de login. |
| **Firewall** | Sistema que filtra el tráfico de red según reglas. Bloquea conexiones no autorizadas. | Bloquea todo el tráfico al puerto `5432` (PostgreSQL) excepto desde el servidor de app. |
| **HTTPS** | HTTP con cifrado TLS. Todo el tráfico entre cliente y servidor viaja encriptado. | Sin HTTPS, un atacante en la misma WiFi puede leer los tokens enviados en cada request. |
| **TLS/SSL** | Protocolo de cifrado que protege el tráfico en tránsito. Es lo que hace funcionar HTTPS. | El candado en el navegador indica que la conexión usa TLS y los datos viajan cifrados. |
| **JWT** | Token firmado que el servidor entrega al hacer login y el cliente envía en cada petición. | El cliente guarda el JWT y lo envía como `Authorization: Bearer` en cada request a la API. |
| **Token** | Cadena de texto que representa una sesión autenticada. El servidor la verifica sin guardar estado. | Al hacer login, el backend devuelve un token. El frontend lo guarda y lo usa en cada request. |
| **Rate limiting** | Limitar la cantidad de peticiones que un cliente puede hacer en un período de tiempo. | Máximo 5 intentos de login por minuto por IP. Previene ataques de fuerza bruta. |
| **Fuerza bruta** | Ataque que prueba miles de combinaciones de contraseñas hasta dar con la correcta. | Sin rate limiting, un atacante puede probar millones de contraseñas automáticamente. |
| **Man-in-the-middle** | Ataque donde alguien intercepta el tráfico entre cliente y servidor sin que ninguno lo sepa. | En una WiFi pública sin HTTPS, un atacante puede leer todos los datos enviados. |
| **Rol** | Nivel de acceso asignado a un usuario. Define qué puede hacer dentro del sistema. | Rol `admin` puede eliminar usuarios. Rol `user` solo puede ver su propio perfil. |
| **Middleware** | Función que se ejecuta entre la petición y la respuesta para verificar condiciones. | Un middleware de autenticación verifica el token antes de que la petición llegue al handler. |
| **.env** | Archivo con variables de entorno y claves secretas. Nunca debe subirse a GitHub. | Contiene `DB_PASSWORD`, `API_KEY` y otros secretos. Agregarlo a `.gitignore` es obligatorio. |
| **Texto plano** | Datos guardados sin ningún tipo de cifrado. Legibles directamente. | Guardar `mipass123` en la BD es texto plano. Un ataque expone todas las contraseñas. |

---

## 💡 TL;DR

| Concepto | En simple |
|---|---|
| **Autenticación** | Verifica quién eres — login, token |
| **Autorización** | Verifica qué puedes hacer — roles, permisos |
| **Hashing** | Transforma la contraseña en algo irreversible — nunca texto plano |
| **SQL Injection** | Input malicioso que manipula la base de datos — validar siempre |
| **XSS** | JavaScript inyectado que se ejecuta en el navegador de otros usuarios |
| **HTTPS** | Cifra el tráfico — obligatorio en producción |
| **JWT** | Token firmado para autenticación sin estado |
| **Rate limiting** | Limita peticiones para prevenir fuerza bruta |

---

> *"Un developer que no entiende seguridad no está construyendo software… está construyendo problemas."*
