<div align="center">
  <img width="483" height="193" alt="logoh" src="https://github.com/user-attachments/assets/99f1d131-893b-4c3d-ae8a-4776ea4c9706" />
</div>  
<br>
<div align="center">
  <p><strong>Servidor Web Full Stack Portátil, Seguro y Autónomo para Windows</strong></p>
  <p>Desarrollado por <a href="https://rodolfohbaz.info/">Rodolfo Hernández Baz</a> (Pr@fEsOr X) | <a href="https://rhinosecurity.xyz/">Rhino Security Tool's</a></p>
</div>
<div align="center">
   <br>
  <img src="https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/PHP-8.x-777BB4?style=for-the-badge&logo=php&logoColor=white" alt="PHP">
  <img src="https://img.shields.io/badge/SQLite-3-003B57?style=for-the-badge&logo=sqlite&logoColor=white" alt="SQLite">
  <img src="https://img.shields.io/badge/SSL/TLS-Cryptography-FFC107?style=for-the-badge&logo=python&logoColor=black" alt="SSL">
  <img src="https://img.shields.io/badge/Windows-10/11-0078D6?style=for-the-badge&logo=windows&logoColor=white" alt="Windows">
  <img src="https://img.shields.io/badge/Licencia-Propietario-red?style=for-the-badge" alt="Licencia">
</div>


## 📖 ¿Qué es XBunker Http Server?

XBunker Http no es solo un servidor web; es un **entorno de desarrollo y despliegue local completamente aislado**. Está diseñado para desarrolladores Full Stack y profesionales de ciberseguridad que necesitan levantar un servidor robusto en segundos sin ensuciar el registro de Windows, sin instalar XAMPP/WAMP, y sin lidiar con configuraciones complejas de Apache o Nginx.

Con un solo clic, levanta un stack completo (HTTP/HTTPS + PHP 8 + SQLite + Seguridad Avanzada) directamente desde un `.exe` portátil.

---

## ⚙️ Características Principales (Análisis Exhaustivo)

A continuación, se documenta en detalle absoluto cada módulo plano del programa, su propósito técnico y su implementación interna.

### 1. 🐘 Motor PHP 8 + SQLite 3 (Puente CGI Nativo)
*   **Para qué sirve:** Permite ejecutar archivos `.php` dinámicamente y conectarlos a bases de datos SQLite sin instalar nada en el sistema operativo anfitrión.
*   **¿Cómo funciona por dentro?** XBunker no usa `mod_php` como Apache. En su lugar, crea un **puente CGI**. Cuando llega un `.php`, el servidor Python captura la petición, inyecta todas las variables de servidor (`$_SERVER`, `$_POST`, `$_GET`) en variables de entorno del sistema temporalmente, ejecuta `php.exe` en segundo plano, captura el `stdout` (el HTML generado) y se lo devuelve al navegador.
*   **Extensiones activadas automáticamente:** `mysqli`, `pdo_mysql`, `sqlite3`, `pdo_sqlite`, `gd` (para imágenes), `mbstring` (para codificación), `curl`.
*   **Aislamiento de errores:** El `php.ini` se modifica automáticamente al instalarse para apagar `display_errors` en producción y evitar que se filtren rutas del servidor.

### 2. 🔒 HTTPS / SSL Portátil (Cifrado de Extremo a Extremo)
*   **Para qué sirve:** Cifra la comunicación. Es obligatorio si trabajas con APIs que exigen HTTPS, cookies `Secure`, o aplicaciones PWA (Progressive Web Apps).
*   **¿Cómo funciona por dentro?** Utiliza la librería `cryptography` de Python para generar un par de claves RSA-2048 y un certificado X.509 firmado en tiempo de ejecución.
*   **Detalles del Certificado:** Se emite para `localhost`, válido por 365 días, con la organización configurada como "Rhino Security Tool's". El certificado se guarda junto al `.exe` (`cert.pem` y `key.pem`) para persistencia.
*   **Implementación:** Envuelve el socket TCP principal del servidor usando `ssl.SSLContext`.

### 3. 🛡️ WAF (Web Application Firewall) Doble Motor
*   **Para qué sirve:** Filtra y bloquea tráfico malicioso antes de que llegue a tu código PHP o tus archivos estáticos.
*   **Ataques que neutraliza:** 
    *   **SQLi (Inyección SQL):** Bloquea cadenas como `' OR 1=1`, `UNION SELECT`, `DROP TABLE`.
    *   **XSS (Cross-Site Scripting):** Bloquea `<script>`, `onerror=`, `javascript:`.
    *   **LFI/RFI (Path Traversal):** Bloquea `../../etc/passwd`.
    *   **RCE (Ejecución Remota):** Bloquea `; ls`, `| whoami`, `&& whoami`, `xp_cmdshell`.
    *   **XXE (XML External Entity):** Bloquea `<!ENTITY`.
*   **Doble Motor:**
    1.  **Motor Rígido:** Un array interno en Python con decenas de patrones.
    2.  **Motor Personalizable (JSON):** Lee un archivo `xbunker_waf_custom.json` donde puedes poner tus propias cadenas o Expresiones Regulares.
*   **Respuesta:** Si se detecta un ataque, se devuelve un `403 Forbidden` con una página HTML de bloqueo personalizada y se registra en el Log.

### 4. 🐝 Honeypot (Señuelo de Intrusos Inteligente)
*   **Para qué sirve:** Engaña a los escáneres automatizados (como DirBuster o Nmap) para que revelen su IP y se piensen que han encontrado información valiosa.
*   **Rutas monitoreadas:** `.env`, `.git`, `wp-admin`, `wp-config.php`, `.htaccess`, `phpmyadmin`, `backup.sql`, `server-status`, entre otras.
*   **La magia (Psicología del atacante):** En lugar de devolver un `404 Not Found` (que le dice al atacante "eso no existe, sigue buscando"), el Honeypot devuelve un **`200 OK`** con un archivo `.env` falso lleno de credenciales inventadas. El atacante se detiene, creyendo que tuvo éxito, mientras su IP queda registrada en tu Log.

### 5. 🚫 Sistema de Auto-Baneo (Fail2Ban Local)
*   **Para qué sirve:** Castiga automáticamente a los atacantes reincidentes bloqueando su IP a nivel de socket.
*   **¿Cómo funciona por dentro?** Utiliza un algoritmo de **ventana deslizante (Sliding Window)**. Si una IP comete una violación (activa el Honeypot o el WAF), se registra la marca de tiempo (`time.time()`). Si en los próximos `X` segundos (ej. 60s) comete `Y` violaciones (ej. 5), la IP entra a un `Set()` de baneo. A partir de ese momento, cualquier petición de esa IP recibe un `403 Forbidden` inmediato, consumiendo mínimos recursos del servidor.
*   **Gestión Visual:** Panel integrado para ver las IPs baneadas y desbanearlas manualmente con un clic.

### 6. ⏱️ Rate Limiting (Anti-DDoS Local por IP)
*   **Para qué sirve:** Evita que una herramienta de estrés (como Hulk o Slowloris) sature la CPU o RAM de tu máquina local.
*   **¿Cómo funciona?** Cuenta cuántas peticiones hace una IP específica en una ventana de tiempo. Si supera el límite, responde con un `429 Too Many Requests` e inyecta la cabecera HTTP `Retry-After: X` para indicarle al cliente cuántos segundos debe esperar.

### 7. 🔐 Autenticación HTTP Básica Global
*   **Para qué sirve:** Ponerle contraseña a todo el servidor sin tocar una línea de código PHP.
*   **Implementación:** Intercepta la cabecera `Authorization`. Si no viene, responde con `401 Unauthorized` y la cabecera `WWW-Authenticate: Basic realm="XBunker"`, lo que fuerza al navegador a mostrar el pop-up clásico de usuario/contraseña. Decodifica el Base64 y lo compara en memoria.

### 8. 📦 Compresión GZIP Dinámica
*   **Para qué sirve:** Reduce el tamaño de las transferencias hasta un 70%, ahorrando ancho de banda si expones el servidor en la LAN.
*   **Lógica de Negociación:** Revisa si la petición del cliente incluye `Accept-Encoding: gzip`. Si es así, comprime la respuesta (tanto archivos estáticos como la salida del motor PHP) usando `compresslevel=6` y añade la cabecera `Content-Encoding: gzip`.

### 9. 🌐 Modo LAN (Exposición en Red Local 0.0.0.0)
*   **Para qué sirve:** Permite que tu teléfono móvil, una tablet o una PC en la misma red WiFi vean tu servidor.
*   **Diferencia técnica:** Un servidor normal se enlaza a `127.0.0.1` (loopback, solo tú). Al activar LAN, XBunker enlaza el socket a `0.0.0.0` (todas las interfaces de red). Al combo se le añade el Generador de QR para que escanees la IP LAN desde tu móvil.

### 10. 📱 Generador de Código QR Nativo / API
*   **Para qué sirve:** Evita teclear IPs largas en el celular.
*   **Lógica:** Intenta usar la librería `qrcode` de Python de forma 100% local y offline. Si el usuario no la tiene instalada, hace un fallback silencioso a la API pública de `api.qrserver.com`.

### 11. 🧪 Mini Postman Integrado
*   **Para qué sirve:** Probar tus APIs REST (GET, POST, PUT, DELETE, PATCH) sin abrir programas pesados de terceros.
*   **Características:** Permite enviar cuerpos JSON personalizados. Tiene integrado un bypass de verificación SSL (`verify_mode = ssl.CERT_NONE`) para que puedas probar contra tu propio HTTPS local sin que Python lance errores de certificado inválido.

### 12. 💻 Terminal Integrada (Puente `cmd.exe`)
*   **Para qué sirve:** Consola de comandos real embebida en la interfaz.
*   **Implementación:** No es un emulador falso. Es un `subprocess.Popen` real de `cmd.exe` con tuberías (`stdin`, `stdout`, `stderr`). Soporta comandos nativos, `php -v`, `composer install`, `git commit`, etc. Tiene protección contra el comando `exit` para que no se cierre el programa principal por error.

### 13. 🗄️ DB Browser for SQLite Integrado
*   **Para qué sirve:** Administrar bases de datos `.sqlite` visualmente (crear tablas, insertar datos, hacer queries) sin instalar software.
*   **Portabilidad:** Extrae un `.zip` oculto en la carpeta temporal del sistema cada vez que lo llamas, lo ejecuta y le pasa tu carpeta de trabajo como argumento.

### 14. 📋 Sistema de 6 Perfiles de Configuración + CRUD
*   **Para qué sirve:** Cambiar toda la arquitectura de seguridad del servidor en 1 segundo, sin tener que activar 15 checkboxes manualmente.
*   **Perfiles Predeterminados:**
    1.  **Modo Por Defecto:** Servidor virgen (PHP On, todo lo demás Off).
    2.  **Modo Desarrollo:** Apaga errores en PHP, quita seguridad (WAF Off, Rate Off) para que los errores de código del programador no sean bloqueados. Fail2Ban muy permisivo.
    3.  **Modo Producción:** Escudo máximo activo (WAF, Honeypot, Rate Limit, GZIP, HTTPS). Todo lo que un servidor real en internet necesitaría.
    4.  **Modo Máxima Seguridad:** Producción + Autenticación HTTP obligatoria + Rate Limit estrictísimo (30 req/min) + Baneo a los 3 intentos.
    5.  **Modo LAN Compartido:** Optimizado para presentaciones en red WiFi (WAF On, GZIP On, LAN On).
    6.  **Modo Testing / API:** Apaga toda la seguridad y sube los límites a topes (10,000 peticiones, Fail2Ban en 999) para hacer pruebas de carga o probar APIs sin interrupciones.
*   **Gestión CRUD:** Puedes crear perfiles nuevos con la configuración actual, duplicar uno existente para hacer variantes, eliminarlos o restaurar los 6 originales de fábrica. Se guardan en `xbunker_perfiles.json`.

### 15. 🔔 Alertas Sonoras Asíncronas
*   **Para qué sirve:** Notificación auditiva cuando el WAF o el Honeypot se activan, ideal si estás programando y no estás mirando la pantalla del Log.
*   **Implementación:** Usa `winsound` en un hilo separado (`threading.Thread`) con el flag `SND_ASYNC` para que el sonido no congele el hilo principal de la interfaz gráfica ni el hilo del servidor.

### 16. 🔢 Gestión Avanzada de Puertos
*   **Para qué sirve:** Evitar el error común "Port 8080 is already in use".
*   **Modo Automático:** Si no lo forzas, XBunker usa un socket temporal vacío para preguntarle al SO qué puerto está libre y lo usa.
*   **Modo Manual:** Permite forzar el puerto 80 (HTTP estándar) o 443 (HTTPS estándar). Si el puerto está ocupado, el programa hace un test de colisión (`socket.bind`) antes de intentar levantar el servidor y te muestra un mensaje claro si falla.

### 17. 📄 Generador Automático de Estructura
*   **Para qué sirve:** Crear un "Hola Mundo" visual al instante.
*   **Qué genera:** Un `index.php` con HTML/CSS moderno responsive mostrando un dashboard de las capacidades de XBunker, un `.htaccess` con reglas de URL Rewrite básicas (`RewriteRule ^(.*)$ index.php?url=$1`), y copia tu logo al directorio.

---

## 🏗️ Arquitectura del Sistema (Para Developers)

Si eres desarrollador, es útil entender cómo se estructura XBunker internamente:

1.  **Hilo Principal (GUI):** Corre `tkinter` (la interfaz gráfica). Nunca se bloquea.
2.  **Hilo Daemon (Servidor Web):** Al presionar "Iniciar", se lanza un `threading.Thread` que ejecuta `socketserver.TCPServer.serve_forever()`.
3.  **Manejador de Peticiones (Handler):** Cada petición HTTP es atendida por una instancia de `ManejadorPHP` (que hereda de `SimpleHTTPRequestHandler`). Esta clase es inyectada con una referencia a la clase principal (`app_instance`) para poder leer las variables de seguridad (WAF, Ban list, etc.) en tiempo real.
4.  **Subprocesos CGI:** Cuando llega un `.php`, el *Handler* no lo procesa él, abre un `subprocess.run([php.exe])`, le pasa las variables de entorno y espera máximo 30 segundos (Timeout).
5.  **Thread de Terminal:** La consola integrada es otro hilo daemon que se dedica a leer el `stdout` del proceso `cmd.exe` e inyectarlo en un widget `ScrolledText`.

---

## 🚀 Guía de Inicio Rápido

1.  Descarga `XBunkerHttp.exe`.
2.  **Ejecuta como Administrador** (solo la primera vez, para instalar PHP 8 en `C:\php`).
3.  Selecciona tu carpeta de proyecto con `📁 Seleccionar Carpeta Web`.
4.  (Opcional) Elige un **Perfil** del menú desplegable (ej. "Modo Desarrollo").
5.  Presiona `▶️ Iniciar`.
6.  Haz clic en el enlace o en `📱 QR` para abrir en tu navegador/celular.

---

## 📁 Archivos y Rutas Importantes

Al usar XBunker, pueden aparecer estos archivos junto al `.exe` o en tu sistema:
*   `xbunker_perfiles.json`: Tus perfiles de configuración personalizados.
*   `xbunker_waf_custom.json`: Tus reglas personalizadas del firewall.
*   `cert.pem` & `key.pem`: Tu certificado SSL autofirmado.
*   `C:\php\`: El motor PHP 8 aislado (manipulado automáticamente por la app).
*   `%TEMP%\XBunker_DBBrowser\`: DB Browser extraído temporalmente.

---

## ⚠️ Requisitos

*   **OS:** Windows 10 / 11 (Requiere APIs nativas de Windows como `winreg` y `winsound`).
*   **Permisos:** Administrador (Solo para la instalación automática de PHP en `C:\php` y añadirlo al PATH del sistema).
*   **Internet:** Opcional. Solo se requiere si usas el Generador de QR sin tener la librería `qrcode` instalada, o si tu PHP necesita descargar paquetes vía Composer.

---

<div align="center">
  <p><strong>Rhino Security Tool's</strong></p>
  <p>Software diseñado para agilizar el desarrollo y proteger aplicaciones locales.</p>
  <p>Desarrollado con ❤️ por <a href="https://rodolfohbaz.info/">Rodolfo Hernández Baz</a></p>
  <p><a href="https://rodolfohbaz.info/">rodolfohbaz.info</a> | <a href="https://rhinosecurity.xyz/">rhinosecurity.xyz</a></p>
</div>
```
