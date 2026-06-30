<div align="center">
  <img width="483" height="193" alt="logoh" src="https://github.com/user-attachments/assets/99f1d131-893b-4c3d-ae8a-4776ea4c9706" />
</div>  
<br>
<div align="center">
  <p><strong>Servidor Web Full Stack Portátil, Seguro y Autónomo para Windows</strong></p>
  <p>Desarrollado por <a href="https://rodolfohbaz.info/">Rodolfo Hernández Baz</a> (Pr@fEsOr X) | <a href="https://rhinosecurity.xyz/">Rhino Security Tool's</a></p>
</div>
<div align="center">
  <a href="https://rhinosecurity.xyz/XtoolsR/VisitCounterv1/api/stats.php?code=2KfbnHR4" target="_blank" rel="noopener"><img src="https://rhinosecurity.xyz/XtoolsR/VisitCounterv1/counter.php?code=2KfbnHR4" alt="Visitas:" style="border:0"></a>
   <br>
  <img src="https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/PHP-8.x-777BB4?style=for-the-badge&logo=php&logoColor=white" alt="PHP">
  <img src="https://img.shields.io/badge/SQLite-3-003B57?style=for-the-badge&logo=sqlite&logoColor=white" alt="SQLite">
  <img src="https://img.shields.io/badge/SSL/TLS-Cryptography-FFC107?style=for-the-badge&logo=python&logoColor=black" alt="SSL">
  <img src="https://img.shields.io/badge/Windows-10/11-0078D6?style=for-the-badge&logo=windows&logoColor=white" alt="Windows">
  <img src="https://img.shields.io/badge/Licencia-Propietario-red?style=for-the-badge" alt="Licencia">
</div>


---

## 📖 ¿Qué es XBunker Http Server?

XBunker Http no es solo un servidor web; es un **entorno de desarrollo y despliegue local completamente aislado**. Está diseñado para desarrolladores Full Stack y profesionales de ciberseguridad que necesitan levantar un servidor robusto en segundos sin ensuciar el registro de Windows, sin instalar XAMPP/WAMP, y sin lidiar con configuraciones complejas de Apache o Nginx.

Con un solo clic, levanta un stack completo (HTTP/HTTPS + PHP 8 + SQLite + Seguridad Avanzada) directamente desde un `.exe` portátil.

---

## ⚙️ Características Principales (Análisis Exhaustivo)

A continuación, se documenta en detalle absoluto cada módulo plano del programa, su propósito técnico y su implementación interna.

### 1. 🐘 Motor PHP 8 + SQLite 3 (Puente CGI Nativo)
*   **Para qué sirve:** Permite ejecutar archivos `.php` dinámicamente y conectarlos a bases de datos SQLite sin instalar nada en el sistema operativo anfitrión.
*   **¿Cómo funciona por dentro?** Crea un **puente CGI**. Cuando llega un `.php`, el servidor captura la petición, inyecta las variables de servidor en variables de entorno temporalmente, ejecuta `php.exe` en segundo plano, captura el `stdout` y se lo devuelve al navegador.
*   **Extensiones activadas:** `mysqli`, `pdo_mysql`, `sqlite3`, `pdo_sqlite`, `gd`, `mbstring`, `curl`.

### 2. 🔒 HTTPS / SSL Portátil (Cifrado de Extremo a Extremo)
*   **Para qué sirve:** Cifra la comunicación. Es obligatorio para APIs que exigen HTTPS, cookies `Secure`, o aplicaciones PWA.
*   **¿Cómo funciona por dentro?** Genera un par de claves RSA-2048 y un certificado X.509 usando `cryptography`. Se guarda en `cert.pem` y `key.pem` junto al `.exe` para persistencia, válido por 365 días.

### 3. 🛡️ WAF (Web Application Firewall) Doble Motor
*   **Para qué sirve:** Filtra y bloquea tráfico malicioso antes de que llegue a tu código PHP.
*   **Ataques que neutraliza:** SQLi (`' OR 1=1`), XSS (`<script>`), Path Traversal (`../../`), RCE (`; whoami`), XXE (`<!ENTITY`).
*   **Doble Motor:** Motor rígido interno + Motor personalizable mediante archivo `xbunker_waf_custom.json`.

### 4. 🐝 Honeypot (Señuelo de Intrusos Inteligente)
*   **Para qué sirve:** Engaña a escáneres automáticos para que revelen su IP creyendo que tuvieron éxito.
*   **¿Cómo funciona?** Monitorea rutas como `.env`, `.git`, `wp-admin`. En lugar de un `404`, devuelve un `200 OK` con un archivo `.env` falso lleno de credenciales inventadas, registrando la IP del atacante.

### 5. 🚫 Sistema de Auto-Baneo (Fail2Ban Local)
*   **Para qué sirve:** Castiga automáticamente a los atacantes reincidentes bloqueando su IP a nivel de socket.
*   **¿Cómo funciona?** Usa un algoritmo de **ventana deslizante (Sliding Window)**. Si una IP comete `X` violaciones en `Y` segundos, entra a un `Set()` de baneo y recibe un `403 Forbidden` inmediato.

### 6. ⏱️ Rate Limiting (Anti-DDoS Local por IP)
*   **Para qué sirve:** Evita que herramientas de estrés saturen la CPU o RAM de tu máquina local.
*   **¿Cómo funciona?** Cuenta peticiones por IP en una ventana de tiempo. Si supera el límite, responde con `429 Too Many Requests` e inyecta `Retry-After: X`.

### 7. 🔐 Autenticación HTTP Básica Global
*   **Para qué sirve:** Ponerle contraseña a todo el servidor sin tocar el código PHP.
*   **Implementación:** Intercepta `Authorization`, fuerza el pop-up clásico del navegador, decodifica el Base64 y compara en memoria.

### 8. 📦 Compresión GZIP Dinámica
*   **Para qué sirve:** Reduce el tamaño de las transferencias hasta un 70%.
*   **Lógica:** Revisa `Accept-Encoding: gzip`. Si existe, comprime respuestas estáticas y salidas PHP usando `compresslevel=6`.

### 9. 🌐 Modo LAN (Exposición en Red Local 0.0.0.0)
*   **Para qué sirve:** Permite que móviles u otras PCs en tu WiFi vean tu servidor.
*   **Diferencia técnica:** Enlaza el socket a `0.0.0.0` en lugar de `127.0.0.1`.

### 10. 📱 Generador de Código QR Nativo / API
*   **Para qué sirve:** Evitar teclear IPs largas en el celular.
*   **Lógica:** Usa librería `qrcode` local (100% offline). Si no existe, hace fallback a `api.qrserver.com`.

### 11. 🧪 Mini Postman Integrado
*   **Para qué sirve:** Probar APIs REST (GET, POST, PUT, DELETE, PATCH) sin abrir programas pesados.
*   **Características:** Envío de JSON y bypass de verificación SSL nativo para probar contra tu propio HTTPS local.

### 12. 💻 Terminal Integrada (Puente `cmd.exe`)
*   **Para qué sirve:** Consola real embebida en la UI.
*   **Implementación:** `subprocess.Popen` real de `cmd.exe` con tuberías. Protección contra el comando `exit` para no cerrar la app.

### 13. 🗄️ DB Browser for SQLite Integrado
*   **Para qué sirve:** Administrar bases de datos `.sqlite` visualmente sin instalar software.
*   **Portabilidad:** Extrae un `.zip` oculto a la carpeta temporal y lo ejecuta apuntando a tu carpeta de trabajo.

### 14. 📋 Sistema de 6 Perfiles de Configuración + CRUD
*   **Para qué sirve:** Cambiar toda la arquitectura del servidor en 1 segundo.
*   **Perfiles:** Por Defecto, Desarrollo, Producción, Máxima Seguridad, LAN Compartido, Testing API.
*   **Gestión:** Crear, Duplicar, Eliminar y Restaurar originales de fábrica. Se guardan en `xbunker_perfiles.json`.

### 15. 🔔 Alertas Sonoras Asíncronas
*   **Para qué sirve:** Notificación auditiva instantánea de eventos de seguridad.
*   **Implementación:** Usa `winsound` en un hilo separado (`SND_ASYNC`) para no congelar la UI.

### 16. 🔢 Gestión Avanzada de Puertos
*   **Para qué sirve:** Evitar el error "Port 8080 is already in use".
*   **Modos:** Automático (busca puerto libre vía socket temporal) o Manual (fuerza el 80/443 con test de colisión previo).

### 17. 📄 Generador Automático de Estructura
*   **Para qué sirve:** Crear un "Hola Mundo" visual al instante.
*   **Qué genera:** `index.php`, `.htaccess` con URL Rewrite y copia tu logo al directorio.

### 18. 🪟 Modo Bandeja del Sistema (System Tray Portátil) *NUEVA*
*   **Para qué sirve:** Permite que XBunker funcione como un servicio de fondo sin ocupar espacio en la barra de tareas de Windows mientras programas o usas otras herramientas.
*   **¿Cómo funciona por dentro?** Al presionar el botón de cerrar "X" tradicional de la ventana principal, en lugar de terminar el proceso (`root.destroy`), el programa ejecuta `root.withdraw()` (oculta la ventana) e instancia una pequeña ventana flotante "Top-Most" en la esquina inferior derecha de la pantalla.
*   **Características del widget:** Muestra un semáforo visual verde [ON] o rojo [OFF] indicando si el servidor está corriendo, y contiene dos botones: "Mostrar" (para restaurar la interfaz completa) y "X" (para cerrar y matar el proceso definitivamente).

### 19. 🌐 Editor Visual de CORS (Cross-Origin Resource Sharing) *NUEVA*
*   **Para qué sirve:** Resuelve de forma inmediata el molesto error de consola `Blocked by CORS policy` que ocurre cuando un frontend moderno (React, Vue, Angular, Vanilla JS en otro puerto) intenta consumir tu API PHP local.
*   **¿Cómo funciona por dentro?** Al activarse, el servidor intercepta el método `end_headers()` de *todas* las peticiones e inyecta dinámicamente las cabeceras HTTP necesarias:
    *   `Access-Control-Allow-Origin`
    *   `Access-Control-Allow-Methods`
    *   `Access-Control-Allow-Headers`
*   **Solución Preflight:** Modifica específicamente el método `do_OPTIONS()` para que cuando el navegador envíe una petición de "verificación" CORS, el servidor responda con un `200 OK` correcto incluyendo `Access-Control-Max-Age: 86400`, evitando que la petición real sea bloqueada.

### 20. 🎨 Páginas de Error Personalizables (Custom 404 / 403 / 500) *NUEVA*
*   **Para qué sirve:** Evita que los usuarios vean las feas páginas de error por defecto de Python/Windows cuando algo falla, permitiendo mantener la identidad visual de tu proyecto incluso en los errores (UX/UI).
*   **¿Cómo funciona por dentro?** Se modificó la función nativa `send_error(code, message)` del servidor. Antes de renderizar el error estándar, el código revisa si existe un archivo llamado `{code}.html` (ej. `404.html`) en la raíz de tu carpeta web. Si existe, lo lee en modo binario, envía el código de estado HTTP correspondiente y lo devuelve al cliente. Si no existe, se comporta con normalidad.
*   **Archivos soportados:** Crea `404.html`, `403.html`, `401.html`, `429.html`, `500.html`, etc., en tu carpeta y XBunker los usará automáticamente.

### 21. 🐛 Visor de Errores PHP en Tiempo Real (Tail Log) *NUEVA*
*   **Para qué sirve:** Permite al desarrollador ver los errores críticos de PHP (`Fatal error`, `Parse error`, `Warning`) en una pestaña visual dedicada, **sin mostrarlos en el navegador** (lo cual es un riesgo de seguridad).
*   **¿Cómo funciona por dentro?** 
    1. Durante la instalación de PHP, XBunker modifica automáticamente el `php.ini` para apuntar `error_log` a un archivo temporal oculto en la carpeta temporal de Windows (`%TEMP%\xbunker_php_errors.log`).
    2. Al presionar "Iniciar Servidor", se lanza un hilo de monitoreo en segundo plano (estilo comando `tail -f` de Linux).
    3. Este hilo revisa el tamaño del archivo cada 2 segundos. Si detecta crecimiento, lee las líneas nuevas, filtra las que contienen palabras clave como "Fatal", "Parse" o "Warning", y las inyecta con color rojo en una nueva pestaña llamada "🐛 Errores PHP" debajo del Log principal.
    4. Al detener el servidor, el hilo se mata de forma segura.

## 22. 🌐 Subdominios personalizados locales (modifica hosts)
Permite acceder a tu servidor local usando nombres de dominio personalizados (ej. `miapp.local`) en lugar de usar la IP `127.0.0.1`.

* **Uso:** Haz clic en el botón `Subdominios`, escribe el nombre (ej. `rodolfohbaz.localhost`), elige la IP y agrega.
* **Flush DNS:** Limpia la caché DNS de Windows automáticamente al agregar o eliminar para que los cambios surtan efecto al instante.
* **Limpieza:** Incluye opción para eliminar de forma segura solo las entradas creadas por XBunker en el archivo `hosts`.
> ⚠️ **Requiere ejecutar XBunker como Administrador** para poder modificar el archivo `C:\Windows\System32\drivers\etc\hosts`.



## 23. 🪟 Inicio automático con Windows
Permite que XBunker Http se ejecute automáticamente al encender o iniciar sesión en tu computadora.

* **Uso:** Haz clic en el botón `Inicio Auto` para activar o desactivar la función.
* **Funcionamiento:** Crea una entrada segura en el registro de Windows para arrancar con el sistema.
> ⚠️ **Nota:** Si mueves el archivo `.exe` a otra carpeta, deberás desactivar y volver a activar esta opción para actualizar la nueva ruta en el registro.

---

## 🏗️ Arquitectura del Sistema (Para Developers)

Si eres desarrollador, es útil entender cómo se estructura XBunker internamente:

1.  **Hilo Principal (GUI):** Corre `tkinter` (la interfaz gráfica). Nunca se bloquea.
2.  **Hilo Daemon (Servidor Web):** Al presionar "Iniciar", se lanza un `threading.Thread` que ejecuta `socketserver.TCPServer.serve_forever()`.
3.  **Hilo de Errores PHP:** Vigila el archivo `error_log` en segundo plano sin consumir CPU.
4.  **Manejador de Peticiones (Handler):** Hereda de `SimpleHTTPRequestHandler`. Se inyectan las variables de seguridad. Se sobreescriben `end_headers()` (para CORS) y `send_error()` (para Custom HTML).
5.  **Subprocesos CGI:** Cuando llega un `.php`, abre un `subprocess.run([php.exe])`, pasa las variables de entorno y espera máximo 30 segundos.
6.  **Thread de Terminal:** Consola CMD real conectada por tuberías.

---

## 🚀 Guía de Inicio Rápido

1.  Descarga `XBunkerHttp.exe`.
2.  **Ejecuta como Administrador** (solo la primera vez, para instalar PHP 8).
3.  Selecciona tu carpeta de proyecto.
4.  Elige un **Perfil** del menú desplegable (ej. "Modo Desarrollo").
5.  Presiona `▶️ Iniciar`.
6.  *Tip:* Cierra la ventana con la "X" y el servidor seguirá corriendo en la Bandeja del Sistema.

---

## 📁 Archivos y Rutas Importantes

Al usar XBunker, se generan o usan estos archivos:
*   `xbunker_perfiles.json`: Tus perfiles de configuración personalizados.
*   `xbunker_waf_custom.json`: Tus reglas personalizadas del WAF.
*   `cert.pem` & `key.pem`: Tu certificado SSL autofirmado.
*   `%TEMP%\xbunker_php_errors.log`: Log temporal de errores críticos de PHP (leído por la pestaña 🐛).
*   `C:\php\`: Motor PHP 8 aislado.
*   `404.html` / `403.html` / `500.html`: (Opcional) Los creas en tu proyecto para personalizar errores.

---

## ⚠️ Requisitos del Sistema

*   **OS:** Windows 10 / 11 (Requiere APIs nativas como `winreg`, `winsound`, `ctypes`).
*   **Permisos:** Administrador (Solo para instalar PHP en `C:\php`).
*   **Internet:** Opcional. Solo si no tienes la librería `qrcode` instalada para generar QRs, o si Composer necesita descargar paquetes.

---

<div align="center">
  <p><strong>Rhino Security Tool's</strong></p>
  <p>Software diseñado para agilizar el desarrollo y proteger aplicaciones locales.</p>
  <p>Desarrollado con ❤️ por <a href="https://rodolfohbaz.info/">Rodolfo Hernández Baz</a></p>
  <p><a href="https://rodolfohbaz.info/">rodolfohbaz.info</a> | <a href="https://rhinosecurity.xyz/">rhinosecurity.xyz</a></p>
</div>
```
