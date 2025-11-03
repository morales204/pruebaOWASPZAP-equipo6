# 🔐 Sistema de Login con Flask y SQLite

Sistema de autenticación completo desarrollado con Flask y SQLite. Incluye registro de usuarios, login, logout y dashboard protegido.

## 🌟 Características

- ✅ Registro de usuarios con validación
- ✅ Sistema de login seguro
- ✅ Contraseñas hasheadas con Werkzeug
- ✅ Base de datos SQLite
- ✅ Sesiones de usuario
- ✅ Rutas protegidas con decoradores
- ✅ Interfaz moderna con Bootstrap 5
- ✅ Mensajes flash para feedback al usuario
- ✅ Diseño responsivo
- ✅ Variables de entorno con .env

## 📋 Requisitos

- Python 3.7 o superior
- pip (gestor de paquetes de Python)

## 🚀 Instalación

1. **Navega al directorio del proyecto:**
```bash
cd flask-login-app
```

2. **Crea un entorno virtual (recomendado):**
```bash
python3 -m venv venv
```

3. **Activa el entorno virtual:**

En Linux/Mac:
```bash
source venv/bin/activate
```

En Windows:
```bash
venv\Scripts\activate
```

4. **Instala las dependencias:**
```bash
pip install -r requirements.txt
```

5. **Configura las variables de entorno:**

El archivo `.env` ya está incluido con valores de desarrollo. Para producción, genera una clave secreta:

```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

Y actualiza el valor de `SECRET_KEY` en el archivo `.env`

## ▶️ Ejecutar la aplicación

1. **Inicia el servidor:**
```bash
python app.py
```

2. **Abre tu navegador y ve a:**
```
http://localhost:5000
```

La base de datos SQLite (`usuarios.db`) se creará automáticamente la primera vez que ejecutes la aplicación.

## 📱 Uso

### Registro
1. Haz clic en "Regístrate aquí" en la página de login
2. Completa el formulario con:
   - Nombre de usuario (único)
   - Email (único)
   - Contraseña (mínimo 6 caracteres)
   - Confirmación de contraseña
3. Haz clic en "Registrarme"

### Login
1. Ingresa tu nombre de usuario
2. Ingresa tu contraseña
3. Haz clic en "Iniciar Sesión"

### Dashboard
Una vez autenticado, verás:
- Información de tu cuenta
- ID de usuario
- Nombre de usuario
- Email registrado

### Logout
Haz clic en "Cerrar Sesión" en la barra de navegación o en el botón del dashboard.

## 📁 Estructura del Proyecto

```
flask-login-app/
│
├── app.py                 # Aplicación principal de Flask
├── requirements.txt       # Dependencias del proyecto
├── README.md             # Este archivo
├── .env                  # Variables de entorno (no subir a git)
├── .env.example          # Plantilla de variables de entorno
├── .gitignore            # Archivos ignorados por git
├── usuarios.db           # Base de datos SQLite (se crea automáticamente)
│
└── templates/            # Plantillas HTML
    ├── base.html         # Plantilla base
    ├── login.html        # Página de login
    ├── registro.html     # Página de registro
    └── dashboard.html    # Panel de usuario
```

## 🔒 Seguridad

- Las contraseñas se almacenan hasheadas usando `pbkdf2:sha256`
- Uso de sesiones seguras de Flask
- Validación de datos en el servidor
- Protección de rutas con decorador `@login_required`
- Verificación de usuarios únicos
- Variables sensibles en archivo `.env` (no incluido en git)
- **Escaneos automáticos de seguridad con OWASP ZAP**

Para más información sobre seguridad, consulta el archivo [SECURITY.md](SECURITY.md).

### 🔍 Pruebas de Seguridad

Este proyecto incluye un workflow de GitHub Actions (`.github/workflows/owasp-zap-scan.yml`) que ejecuta automáticamente escaneos de seguridad con OWASP ZAP.

**Ejecución manual local:**
```bash
# Con Docker
docker pull zaproxy/zap-stable
python app.py &
docker run -t zaproxy/zap-stable zap-baseline.py -t http://host.docker.internal:5000 -r report.html
```

Los reportes se generan automáticamente en cada push y pull request.

## 🛠️ Tecnologías Utilizadas

- **Flask** - Framework web de Python
- **Flask-SQLAlchemy** - ORM para base de datos
- **SQLite** - Base de datos ligera
- **Werkzeug** - Utilidades para hashing de contraseñas
- **python-dotenv** - Gestión de variables de entorno
- **Bootstrap 5** - Framework CSS
- **Font Awesome** - Iconos

## ⚙️ Configuración

### Variables de Entorno

El proyecto usa un archivo `.env` para configuración. Variables disponibles:

- `SECRET_KEY`: Clave secreta de Flask (cambiar en producción)
- `FLASK_ENV`: Entorno de ejecución (development/production)
- `FLASK_DEBUG`: Modo debug (True/False)
- `DATABASE_URI`: URI de la base de datos

Para producción, asegúrate de:
1. Cambiar `SECRET_KEY` por una clave segura
2. Establecer `FLASK_ENV=production`
3. Establecer `FLASK_DEBUG=False`

## 📝 Notas

- La base de datos se crea automáticamente al ejecutar la aplicación
- Por defecto, la aplicación corre en modo debug (desactivar en producción)
- El servidor escucha en todas las interfaces (0.0.0.0) en el puerto 5000

## 🐛 Solución de Problemas

**Error: No module named 'flask'**
- Asegúrate de haber activado el entorno virtual e instalado las dependencias

**Error: Address already in use**
- El puerto 5000 está ocupado. Cambia el puerto en `app.py` o cierra la aplicación que lo usa

**No se pueden crear usuarios**
- Verifica que tengas permisos de escritura en el directorio del proyecto
- La base de datos se crea en el mismo directorio que `app.py`

## 📄 Licencia

Este proyecto es de código abierto y está disponible para uso educativo.

---

¡Disfruta usando tu sistema de login! 🎉
