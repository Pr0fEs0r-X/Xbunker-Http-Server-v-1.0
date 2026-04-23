<div align="center">
  <img width="483" height="193" alt="logoh" src="https://github.com/user-attachments/assets/99f1d131-893b-4c3d-ae8a-4776ea4c9706" />
</div>  
<br>
<div align="center">
  <img src="https://img.shields.io/badge/Version-1.0-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Status-Stable-brightgreen?style=for-the-badge" />
  <img src="https://img.shields.io/badge/OS-Windows_10/11-0078D6?style=for-the-badge&logo=windows" />
  <img src="https://img.shields.io/badge/Language-Python-yellow?style=for-the-badge&logo=python" />
  <img src="https://img.shields.io/badge/PHP-8.x-777BB4?style=for-the-badge&logo=php" />
</div>

# 🛡️ XBunker HTTP v 1.0 (Full Stack)

**Servidor Web Portátil de Seguridad y Desarrollo Local**. Una herramienta de ingeniería inversa y desarrollo web ultraligera diseñada para levantar entornos LAMP/LEMP completos en cuestión de segundos, sin exponer la red del usuario y sin dejar rastros en el sistema operativo.

---

## 🌟 Características Principales

*   🐘 **Motor PHP 8 Nativo:** Instalador automático silencioso. No requiere XAMPP, WAMP ni configuraciones manuales.
*   🗄️ **Base de Datos Integrada:** Soporte completo para **SQLite 3** sin servicios externos.
*   🔒 **HTTPS / SSL Portátil:** Genera certificados de seguridad X.509 internamente. **No requiere OpenSSL en el sistema**.
*   ♻️ **URL Rewrite:** Soporte para rutas amigables nativas (Compatible con Laravel, CodeIgniter, etc.).
*   🧪 **Mini Postman Integrado:** Cliente HTTP visual para probar APIs y endpoints sin salir de la app.
*   🗄️ **Gestor de Base de Datos Visual:** Integración nativa con *DB Browser for SQLite* para manejar datos gráficamente.
*   🎼️ **100% Aislado (Loopback):** Escucha exclusivamente en `127.0.0.1`. Cero exposición a redes locales o internet.
*   🧹 **Sin Residuos:** Apagado instantáneo. Al cerrar la app, el servidor se mata a sí mismo sin dejar puertos abiertos.
*   🎨 **Interfaz Moderna:** Diseño "Dark Mode" sin bordes nativos de Windows, personalizable y arrastrable.

---

## 🧠 Tecnologías Utilizadas y Cómo Funcionan

XBunker HTTP funciona como un orquestador extremadamente ligero, evitando dependencias pesadas como Apache o Nginx.

| Componente | Tecnología | ¿Cómo se usa en XBunker? |
| :--- | :--- | :--- |
| **Interfaz Gráfica** | Python 3 + Tkinter | Genera una ventana sin bordes nativos, maneja el entorno gráfico, los eventos de arrastre y los logs en tiempo real. |
| **Servidor Web** | `http.server` & `socketserver` | Crea un servidor TCP dentro de Python vinculado al `127.0.0.1` en un puerto dinámico. |
| **Procesamiento PHP** | `subprocess` (CGI Nativo) | Cuando llega un `.php`, Python construye las variables de entorno CGI (`REQUEST_METHOD`, `QUERY_STRING`, etc.), ejecuta `php.exe` en un subproceso y devuelve el `stdout` al navegador. |
| **Base de Datos** | SQLite 3 + PDO | PHP se conecta a archivos `.db` locales de forma nativa. El programa incluye un gestor visual externo para manipular estas bases de datos sin escribir código. |
| **Certificados SSL** | `cryptography` | En lugar de llamar a binarios de Windows, Python genera matemáticamente una llave RSA y un certificado X.509 autofirmado en memoria y los aplica al socket del servidor. |
| **Enrutamiento** | `urllib.parse` (Python) | Un parser personalizado intercepta las peticiones. Si la ruta no es un archivo real (ej: `/usuario/1`), la redirige internamente a `index.php` simulando un `.htaccess`. |
| **Cliente API** | `urllib.request` | El Mini Postman construye peticiones HTTP, inyecta cabeceras JSON y maneja la respuesta, ignorando errores de certificados autofirmados mediante `ssl.create_default_context()`. |
| **Gestión de Archivos** | `zipfile` + `tempfile` | Extrae PHP y DB Browser directamente desde archivos `.zip` embebidos en tiempo de ejecución hacia carpetas temporales de Windows, logrando portabilidad total con un solo `.exe`. |

---

## 📖 Datos de Uso y Guía Rápida

### Primer inicio (Requiere Administrador)
1. Haz clic derecho sobre `XBunkerHTTP.exe` y selecciona **"Ejecutar como Administrador"** (Solo la primera vez, para instalar PHP 8 en `C:\php` y registrarlo en el PATH del sistema).
2. El programa se encargará de descomprimir, configurar el `php.ini` y habilitar las extensiones necesarias.

### Uso cotidiano
1. **Seleccionar Directorio:** Haz clic en "📁 Seleccionar Carpeta Web" y elige la raíz de tu proyecto.
2. **Configurar Opciones:**
   * **🐘 PHP + SQLite:** Actívalo para ejecutar archivos `.php` y conectarte a bases de datos.
   * **🔒 HTTPS:** Actívalo para forzar el protocolo seguro (útil para probar PWA, WebRTC o APIs seguras).
3. **Iniciar Servidor:** Presiona `▶️ Iniciar`. El programa asignará un puerto automáticamente (ej. 8080).
4. **Probar:** Haz clic en el enlace generado en la barra inferior o usa el botón **🧪 Postman** para probar tus endpoints.

### Uso de Herramientas Integradas
* **🗄️ Abrir DB SQLite:** Abre la interfaz gráfica para crear tablas, insertar datos y hacer consultas SQL directamente sobre los archivos `.db` de tu carpeta web.
* **🎼 Abrir Composer:** Abre una terminal temporal apuntando a tu carpeta para instalar dependencias de PHP.
* **🧪 Mini Postman:** Abre una ventana flotante para enviar métodos `GET`, `POST`, `PUT`, `DELETE` con cuerpos JSON y analizar las respuestas del servidor.

---

## 👨‍💻 Desarrollador

<div align="center">
  <table>
    <tr>
      <td align="center" width="150">
        <img src="https://img.shields.io/badge/Rodolfo_Hernández_Baz-blue?style=for-the-badge" />
      </td>
    </tr>
    <tr>
      <td align="center">
        <h3>Pr@fEsOr X</h3>
        <p><i>Especialista en Ciberseguridad, Hacking Ético e Ingeniería Inversa</i></p>
      </td>
    </tr>
  </table>
</div>

**Empresa / Organización:**  
🛡️ **Rhino Forensci & Reverse Toolkit**

**Web del Desarrollador:**  
🔗 [rodolfohbaz.info](https://rodolfohbaz.info/)

**Web de la Empresa:**  
🔗 [Rhino Security Tool's](https://rhinosecurity.xyz/)

---

## ⚙️ Notas Técnicas para Compilación

Si deseas compilar el código fuente tú mismo, asegúrate de tener la siguiente estructura y dependencias:

**Dependencias de Python:**
```bash
pip install pyinstaller pillow cryptography
```

**Archivos requeridos en la carpeta del proyecto:**
* `xbunker_http.py` (Código fuente)
* `php-8.5.5.zip` (Binarios de PHP)
* `dbbrowser.zip` (Carpeta portable de DB Browser for SQLite comprimida)
* `composer.phar` (Gestor de paquetes de PHP)
* `logo.png` / `logoh.png` (Recursos gráficos)

**Comando de compilación (Solución al error 'ordinal 380' de OpenSSL):**
Es recomendable usar un archivo `.spec` limpiando el PATH para evitar conflictos de OpenSSL con Git en Windows.

```cmd
pyinstaller --onefile --windowed --name "XBunkerHTTP" --add-data "php-8.5.5.zip;." --add-data "logo.png;." --add-data "logoh.png;." --add-data "composer.phar;." --add-data "dbbrowser.zip;." xbunker_http.py
```

---
<div align="center">
  <sub>Desarrollado con Python 3 | Pensado para la Portabilidad y la Seguridad</sub>
</div>
```
