

<div align="center">
  <img width="400" height="150" alt="logoh" src="https://github.com/user-attachments/assets/597bb62e-e10a-49a3-9b79-82253cafc025" />
 
  # 🛡️ XBunker HTTP v 1.0
  **Servidor Local Estático + PHP Auto-instalable**

  [![Made with Python](https://img.shields.io/badge/Python-3.x-blue?logo=python&logoColor=white)](https://www.python.org/)
  [![Windows](https://img.shields.io/badge/Windows-10/11-0078D6?logo=windows&logoColor=white)](https://www.microsoft.com/windows)
  [![PHP Support](https://img.shields.io/badge/PHP-8.x-777BB4?logo=php&logoColor=white)](https://www.php.net/)
</div>

---

## 📖 Descripción

**XBunker HTTP** es un servidor web de archivos estáticos ultra ligero diseñado para entornos 100% aislados. Funciona exclusivamente bajo el loopback (`127.0.0.1`), lo que garantiza **cero exposición** a redes locales o a Internet.

Su característica principal es la **instalación automática y silenciosa de PHP**. Si el equipo no tiene PHP instalado, el programa se encarga de descomprimirlo, configurarlo (`php.ini`) y añadirlo al sistema automáticamente con un solo clic.

Ideal para pruebas locales, desarrollo web rápido, o presentaciones de proyectos sin necesidad de instalar XAMPP, WAMP o servidores pesados.

---

## ✨ Características Principales

- 🐘 **Soporte PHP Integrado:** Detecta si PHP falta y lo instala automáticamente desde un `.zip` interno.
- 🔒 **Aislamiento Total:** 100% Aislado (Loopback): Solo escucha en 127.0.0.1. Cero exposición a redes locales o Internet.
- ⚡ **Puerto Dinámico:** Evita conflictos asignando un puerto libre automáticamente.
- 🗄️ **SQLite 3 Activado:** Base de datos relacional lista para usar. Crea archivos .db directamente en tu carpeta web sin necesidad de servicios externos.
- 🛑 **Apagado Instantáneo:** Cierra el servidor y mata los hilos sin dejar procesos residuales (Zombies).
- 🎨 **Interfaz Moderna:** Diseño oscuro, personalizado, sin bordes predeterminados de Windows y arrastre de ventana libre.
- 📦 **Totalmente Portátil:** Un solo archivo .exe. Llévalo en tu USB y ejecútalo en cualquier PC con Windows.

---

## 🚀 ¿Cómo usar el programa (.exe)?

Sigue estos sencillos pasos para comenzar a usar XBunker HTTP:

### 1. Descargar y Ejecutar
Descarga el archivo `XBunkerHTTP.exe` desde la sección de [Releases](../../releases).
* **Primera vez (Para usar PHP):** Haz **clic derecho** sobre el `.exe` y selecciona **"Ejecutar como Administrador"**. *(Esto es estrictamente necesario solo la primera vez para que Windows permita instalar PHP en `C:\php`)*.
* **Siguientes veces:** Puedes abrirlo normalmente (doble clic).

### 2. Seleccionar tu Carpeta Web
Haz clic en el botón **📁 Seleccionar Carpeta Web** y elige la carpeta donde tienes tus archivos `index.html` o `.php`.

### 3. Habilitar PHP (Opcional)
Si tu proyecto usa PHP, marca la casilla **🐘 Habilitar soporte PHP**. 
*Si el programa detectó que PHP no existía, ya lo habrá instalado en este paso. Verás un mensaje de "Instalación Completada".*

### 4. Iniciar el Servidor
Haz clic en **▶️ Iniciar**.
* El sistema asignará un puerto libre automáticamente (ej. `8080`).
* Puedes hacer clic directamente sobre el enlace generado en la interfaz **(Enlace: http://127.0.0.1:8080)** para abrir tu navegador.

### 5. Detener el Servidor
Cuando termines, simplemente haz clic en **⏹️ Detener** o cierra la ventana con la "X" roja. El servidor se apagará al instante.

---

## ⚙️ ¿Qué hace el instalador automático de PHP?

Si ejecutas el programa como Administrador y no detecta PHP, XBunker realiza lo siguiente en segundo plano:

1. Extrae el archivo interno `php-8.x.x.zip` hacia `C:\php`.
2. Renombra `php.ini-development` a `php.ini`.
3. Edita automáticamente el `php.ini` para descomentar y habilitar las extensiones más comunes:
   - `mysqli`
   - `pdo_mysql`
   - `gd`
   - `mbstring`
   - `curl`
4. Registra `C:\php` en las **Variables de Entorno (PATH)** de Windows de forma permanente.

*(Si por algún motivo Windows bloquea el PATH por falta de permisos, el programa seguirá funcionando usando la ruta directa `C:\php\php.exe` para esa sesión).*

---

## 🛠️ Para Desarrolladores (Compilar desde el código)

Si deseas compilar el `.exe` tú mismo, asegúrate de tener los requisitos y seguir esta estructura:

### Requisitos:
- Python 3.8+
- `pip install pillow pyinstaller`

### Estructura requerida antes de compilar:
```text
📁 tu_proyecto/
├── xbunkerv10.py          (El código principal)
├── http.ico               (Icono del ejecutable)
├── logo.png               (Imagen del Splash Screen)
├── logoh.png              (Imagen del Header en la UI)
└── php-8.5.5.zip          (Descargado desde php.net)
```

### Comando de compilación:
```bash
pyinstaller --onefile --windowed --icon=http.ico --add-data "logo.png;." --add-data "logoh.png;." --add-data "php-8.5.5.zip;." --name "XBunkerHTTP" xbunkerv10.py
```

---

## ❓ Solución de Problemas (Troubleshooting)

**¿El programa dice "Descomprimiendo PHP" pero no pasa nada o se cierra?**
* **Causa:** Tu Antivirus (especialmente Windows Defender) está bloqueando silenciosamente la creación de la carpeta `C:\php` o el movimiento de archivos.
* **Solución:** Desactiva temporalmente la protección en tiempo real, o añade una exclusión en tu Antivirus para la unidad `C:\` y para el archivo `.exe`.

**¿No me deja ejecutar como Administrador o sale un error de permisos?**
* Asegúrate de que no tengas ninguna otra terminal, explorador de archivos o programa usando la carpeta `C:\php` en ese momento.

**¿El servidor se inicia pero las páginas PHP muestran error 500?**
* Verifica que la instalación automática haya terminado correctamente abriendo `C:\php` y comprobando que exista el archivo `php.exe` y `php.ini`.

---

## 📜 Créditos

Desarrollado como parte del ecosistema de herramientas de ciberseguridad de libre uso.

| Rol | Nombre |
| :--- | :--- |
| **Desarrollador** | **Rodolfo Hernández Baz** / Pr@fEsOr X |
| **Organización** | **Rhino Security Tool´s** |

🌐 **Web del Desarrollador:** [rodolfohbaz.info](https://rodolfohbaz.info/)  
🌐 **Web de la Empresa:** [rhinosecurity.xyz](https://rhinosecurity.xyz/)

---

<div align="center">
  <sub>Construido con 💻 Python y Tkinter. Distribuido para fines educativos y de desarrollo local.</sub>
</div>
```

### Consejos extra para tu GitHub:
1. **Sube las imágenes:** En la líneas donde dice `<img src="https://github.com/tu-usuario/tu-repo/assets/logoh.png".../>`, cambia esa ruta por la URL real de la imagen una vez que la subas a tu repositorio de GitHub (arrastrando la imagen al editar el README en GitHub).
2. **Sube el `.zip` de PHP pesado en "Releases":** En lugar de compilar el `.exe` con el zip adentro (lo cual hará que tu `.exe` pese unos 30-40MB), puedes omitir el `--add-data "php-8.5.5.zip;."` del PyInstaller, y en tu código hacer que el programa se descargue el zip de Internet la primera vez si no lo encuentra. Pero si prefieres tener **todo en 1 solo archivo offline**, está perfecto tal como lo hicimos.
