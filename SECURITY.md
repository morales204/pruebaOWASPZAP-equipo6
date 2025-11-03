# 🔒 Seguridad

Este documento describe las medidas de seguridad implementadas en el proyecto y cómo ejecutar pruebas de seguridad.

## 🛡️ Medidas de Seguridad Implementadas

### Autenticación y Autorización
- ✅ Contraseñas hasheadas con `pbkdf2:sha256` (Werkzeug)
- ✅ Sesiones seguras de Flask con `SECRET_KEY`
- ✅ Decorador `@login_required` para proteger rutas
- ✅ Validación de datos en el servidor
- ✅ Verificación de usuarios únicos (username y email)

### Protección de Datos
- ✅ Variables sensibles en archivo `.env` (no incluido en git)
- ✅ Base de datos SQLite con contraseñas hasheadas
- ✅ Confirmación de contraseña en el registro
- ✅ Mensajes de error genéricos para no revelar información

### Configuración Segura
- ✅ `.gitignore` configurado para excluir archivos sensibles
- ✅ Separación de configuración entre desarrollo y producción
- ✅ SECRET_KEY en variable de entorno

## 🔍 Pruebas de Seguridad con OWASP ZAP

Este proyecto incluye un workflow de GitHub Actions que ejecuta automáticamente escaneos de seguridad con OWASP ZAP.

### Qué es OWASP ZAP?

OWASP ZAP (Zed Attack Proxy) es una herramienta de código abierto para encontrar vulnerabilidades en aplicaciones web. Es uno de los proyectos más activos de OWASP.

### Tipos de Escaneos

1. **Baseline Scan**: Escaneo rápido que busca vulnerabilidades comunes
2. **Full Scan**: Escaneo completo y exhaustivo (toma más tiempo)

### Ejecución Automática

El escaneo se ejecuta automáticamente en los siguientes casos:
- Push a las ramas `main` o `develop`
- Pull requests a `main`
- Cada lunes a las 2 AM (programado)
- Manualmente desde GitHub Actions

### Ejecutar Localmente

#### Usando Docker:

```bash
# Descargar la imagen de OWASP ZAP
docker pull zaproxy/zap-stable

# Iniciar tu aplicación Flask
python app.py

# Ejecutar el escaneo baseline
docker run -t zaproxy/zap-stable zap-baseline.py \
  -t http://host.docker.internal:5000 \
  -r zap-report.html

# Ejecutar el escaneo completo
docker run -t zaproxy/zap-stable zap-full-scan.py \
  -t http://host.docker.internal:5000 \
  -r zap-full-report.html
```

#### Usando OWASP ZAP Desktop:

1. Descarga OWASP ZAP desde [https://www.zaproxy.org/download/](https://www.zaproxy.org/download/)
2. Inicia tu aplicación Flask: `python app.py`
3. Abre OWASP ZAP
4. Configura el objetivo: `http://localhost:5000`
5. Ejecuta el "Automated Scan"

### Interpretación de Resultados

Los reportes se generan en tres formatos:
- **HTML**: Para visualización en navegador
- **JSON**: Para procesamiento programático
- **Markdown**: Para incluir en documentación

#### Niveles de Alerta

- 🔴 **High (Alto)**: Vulnerabilidades críticas que deben solucionarse de inmediato
- 🟠 **Medium (Medio)**: Vulnerabilidades importantes que requieren atención
- 🟡 **Low (Bajo)**: Problemas menores de seguridad
- 🔵 **Informational**: Información sobre la configuración

## 🚨 Vulnerabilidades Comunes a Revisar

### 1. Cross-Site Scripting (XSS)
**Mitigación**: Flask escapa automáticamente las variables en Jinja2

### 2. SQL Injection
**Mitigación**: Uso de SQLAlchemy ORM con consultas parametrizadas

### 3. Cross-Site Request Forgery (CSRF)
**Mitigación**: Para mejorar, considera usar `Flask-WTF` para protección CSRF

### 4. Sesiones Inseguras
**Mitigación**: 
- Usar HTTPS en producción
- Configurar `SESSION_COOKIE_SECURE = True`
- Configurar `SESSION_COOKIE_HTTPONLY = True`
- Configurar `SESSION_COOKIE_SAMESITE = 'Lax'`

### 5. Cabeceras de Seguridad
**Mitigación**: Agregar cabeceras de seguridad HTTP

## 🔧 Recomendaciones para Producción

### 1. Configuración de Flask

```python
# En app.py para producción
app.config['SESSION_COOKIE_SECURE'] = True  # Solo HTTPS
app.config['SESSION_COOKIE_HTTPONLY'] = True  # No accesible por JavaScript
app.config['SESSION_COOKIE_SAMESITE'] = 'Lax'  # Protección CSRF
app.config['PERMANENT_SESSION_LIFETIME'] = 1800  # 30 minutos
```

### 2. Cabeceras de Seguridad

```python
@app.after_request
def set_security_headers(response):
    response.headers['X-Content-Type-Options'] = 'nosniff'
    response.headers['X-Frame-Options'] = 'SAMEORIGIN'
    response.headers['X-XSS-Protection'] = '1; mode=block'
    response.headers['Strict-Transport-Security'] = 'max-age=31536000; includeSubDomains'
    response.headers['Content-Security-Policy'] = "default-src 'self'"
    return response
```

### 3. Rate Limiting

Considera usar `Flask-Limiter` para prevenir ataques de fuerza bruta:

```python
from flask_limiter import Limiter

limiter = Limiter(app, key_func=lambda: request.remote_addr)

@app.route('/login', methods=['POST'])
@limiter.limit("5 per minute")
def login():
    # ...
```

### 4. Validación de Entrada

- Siempre validar y sanitizar entrada del usuario
- Usar validación en el servidor (no solo cliente)
- Limitar longitud de campos

### 5. HTTPS

- Usar siempre HTTPS en producción
- Redirigir automáticamente HTTP a HTTPS
- Usar certificados SSL/TLS válidos

## 📊 Monitoreo de Seguridad

- Revisar los reportes de OWASP ZAP regularmente
- Mantener las dependencias actualizadas (`pip list --outdated`)
- Seguir las alertas de seguridad de GitHub (Dependabot)
- Revisar logs de acceso sospechoso

## 🐛 Reportar Vulnerabilidades

Si encuentras una vulnerabilidad de seguridad, por favor:

1. **NO** abras un issue público
2. Contacta al equipo de desarrollo directamente
3. Proporciona detalles de la vulnerabilidad
4. Espera una respuesta antes de hacer divulgación pública

## 📚 Recursos

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP ZAP Documentation](https://www.zaproxy.org/docs/)
- [Flask Security Best Practices](https://flask.palletsprojects.com/en/latest/security/)
- [Python Security Best Practices](https://python.readthedocs.io/en/latest/library/security_warnings.html)

---

**Última actualización**: Noviembre 2025
