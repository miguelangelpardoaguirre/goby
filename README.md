# 📱 QR Scanner Pro - Sistema de Inventario

Aplicación web profesional para escanear códigos QR y gestionar inventario automáticamente en Google Sheets. Sistema de control de almacén con seguimiento de entradas y despachos.

## 🎯 Características Principales

### ✅ Funcionalidades Core
- ✨ **Escaneo en tiempo real** usando la cámara del dispositivo (móvil o desktop)
- 📊 **Sistema de inventario** con estados: EN ALMACEN → DESPACHADO
- 🔄 **Seguimiento automático** de fechas de entrada y despacho
- 📦 **Gestión por REFERENCIA y SERIAL** extraídos del QR
- 🚫 **Protección de datos** - evita modificar productos ya despachados
- 📈 **Panel de registros** con historial completo
- 📉 **Estadísticas** en tiempo real por estado
- 💾 **Exportación a CSV** de todos los registros
- 🎨 **Interfaz moderna y responsive** que funciona en cualquier dispositivo

### 📋 Formato del Código QR

Los códigos QR deben contener:
```
REFERENCIA|SERIAL
```

**Ejemplo:** `OG971390|202630010002`

### 🔄 Flujo de Trabajo

1. **Primer escaneo**: Registra producto como `EN ALMACEN` con fecha/hora
2. **Segundo escaneo**: Actualiza a `DESPACHADO` con fecha/hora de despacho
3. **Escaneos adicionales**: Muestra advertencia sin modificar datos

### 📋 Metadatos Registrados

Cada escaneo guarda automáticamente:
- ID único secuencial
- REFERENCIA del producto
- SERIAL del producto
- ESTADO (EN ALMACEN / DESPACHADO)
- FECHA_ALMACEN y HORA_ALMACEN
- FECHA_DESPACHO y HORA_DESPACHO (al despachar)

## 🏗️ Arquitectura Técnica

### Stack Tecnológico

**Frontend:**
- HTML5, CSS3, JavaScript (ES6+)
- Librería: `html5-qrcode` v2.3.8 (escaneo QR confiable)
- Diseño responsive con CSS Grid y Flexbox
- Sin frameworks - código vanilla optimizado

**Backend:**
- Node.js + Express
- Google Sheets API v4 (googleapis)
- Autenticación JWT con Service Account
- CORS habilitado para seguridad

**Almacenamiento:**
- Google Sheets (cloud, gratis, compartible)
- Creación automática de encabezados
- Acceso en tiempo real desde cualquier dispositivo
- Sistema de estados para control de inventario

### ¿Por qué Google Sheets?

**Ventajas sobre otras opciones:**

1. ✅ **Accesibilidad total**: Acceso desde cualquier dispositivo con navegador
2. 💰 **Gratuito**: Sin costos de base de datos
3. 🔗 **Compartible**: Múltiples usuarios pueden ver/editar
4. 📊 **Análisis integrado**: Gráficos, fórmulas, pivots nativos
5. 📱 **App móvil**: Google Sheets app para iOS/Android
6. 🔄 **Backup automático**: Google Drive maneja respaldos
7. 🔌 **API robusta**: Integración sencilla y bien documentada

**vs CSV**: Google Sheets permite acceso en tiempo real, mientras CSV requiere descarga/upload.
**vs Excel Online**: Google Sheets tiene mejor API y es más accesible.
**vs Base de datos**: Para este caso de uso, Sheets es más simple y cumple perfectamente.

### Estructura del Proyecto

```
qr-scanner-app/
├── public/                 # Frontend
│   ├── index.html         # Interfaz principal
│   ├── app.js            # Lógica de la aplicación
│   └── styles.css        # Estilos CSS
├── server.js             # Backend Express + Google Sheets API
├── package.json          # Dependencias y scripts
├── .env.example          # Plantilla de configuración
├── .gitignore           # Archivos a ignorar en Git
└── README.md            # Documentación completa
```

## 🚀 Instalación y Configuración

### Prerrequisitos

- Node.js (v16 o superior)
- Cuenta de Google
- Navegador moderno (Chrome, Firefox, Safari, Edge)

### Paso 1: Clonar/Descargar el Proyecto

```bash
# Si tienes Git
git clone <url-del-repositorio>
cd qr-scanner-app

# O descarga el ZIP y extráelo
```

### Paso 2: Instalar Dependencias

```bash
npm install
```

Esto instalará:
- `express`: Framework web
- `googleapis`: Cliente de Google Sheets API
- `cors`: Seguridad para peticiones cross-origin
- `dotenv`: Gestión de variables de entorno
- `body-parser`: Procesamiento de JSON

### Paso 3: Configurar Google Sheets API

#### 3.1 Crear Proyecto en Google Cloud

1. Ve a [Google Cloud Console](https://console.cloud.google.com/)
2. Crea un nuevo proyecto o selecciona uno existente
3. Dale un nombre descriptivo (ej: "QR Scanner App")

#### 3.2 Habilitar Google Sheets API

1. En el menú lateral: **APIs y servicios** → **Biblioteca**
2. Busca "Google Sheets API"
3. Haz clic en **Habilitar**

#### 3.3 Crear Service Account (Cuenta de Servicio)

1. Ve a **APIs y servicios** → **Credenciales**
2. Haz clic en **Crear credenciales** → **Cuenta de servicio**
3. Completa el formulario:
   - Nombre: `qr-scanner-service`
   - Descripción: `Service account para QR Scanner App`
4. Haz clic en **Crear y continuar**
5. En el rol, selecciona: **Editor** (o crea un rol personalizado)
6. Haz clic en **Continuar** y luego **Listo**

#### 3.4 Generar Clave JSON

1. En la lista de cuentas de servicio, haz clic en la que acabas de crear
2. Ve a la pestaña **Claves**
3. Haz clic en **Agregar clave** → **Crear clave nueva**
4. Selecciona **JSON** y haz clic en **Crear**
5. Se descargará un archivo JSON con las credenciales
6. **¡IMPORTANTE!** Guarda este archivo en lugar seguro y NUNCA lo compartas

#### 3.5 Crear Hoja de Cálculo

1. Ve a [Google Sheets](https://sheets.google.com)
2. Crea una nueva hoja de cálculo
3. Dale un nombre (ej: "Escaneos QR")
4. Copia el ID de la URL:
   ```
   https://docs.google.com/spreadsheets/d/[ESTE_ES_EL_ID]/edit
   ```

#### 3.6 Compartir la Hoja con el Service Account

1. En la hoja de cálculo, haz clic en **Compartir**
2. Pega el email de la service account (está en el archivo JSON descargado, campo `client_email`)
3. Asegúrate de darle permisos de **Editor**
4. Desmarca "Notificar a las personas"
5. Haz clic en **Compartir**

### Paso 4: Configurar Variables de Entorno

1. Copia el archivo de ejemplo:
   ```bash
   copy .env.example .env
   ```

2. Abre el archivo `.env` y completa con tus datos:

```env
# ID de tu hoja de cálculo (de la URL)
GOOGLE_SPREADSHEET_ID=1AbC2DeF3GhI4JkL5MnO6PqR7StU8VwX9YzA

# Email de la service account (del archivo JSON)
GOOGLE_CLIENT_EMAIL=qr-scanner-service@tu-proyecto.iam.gserviceaccount.com

# Clave privada (del archivo JSON)
# ⚠️ IMPORTANTE: Mantén los saltos de línea como \n
GOOGLE_PRIVATE_KEY=-----BEGIN PRIVATE KEY-----\nTuClavePrivadaAqui\n-----END PRIVATE KEY-----

# Puerto del servidor (opcional, por defecto 3000)
PORT=3000

# Ambiente (opcional)
NODE_ENV=development
```

**💡 Consejo**: Para obtener la clave privada correctamente:
1. Abre el archivo JSON descargado
2. Copia el valor completo de `private_key` (incluyendo las comillas)
3. Pégalo en el archivo `.env`

### Paso 5: Iniciar la Aplicación

```bash
# Modo desarrollo (reinicio automático con nodemon)
npm run dev

# O modo producción
npm start
```

Verás en consola:
```
✅ Servidor ejecutándose en http://localhost:3000
📊 Ambiente: development
🚀 API lista para recibir solicitudes
```

### Paso 6: Acceder a la Aplicación

1. Abre tu navegador
2. Ve a: `http://localhost:3000`
3. La aplicación pedirá permisos para acceder a la cámara
4. ¡Listo para escanear!

## 📖 Guía de Uso
📦 Sistema de Inventario

**Ver documentación completa:** [SISTEMA_INVENTARIO.md](SISTEMA_INVENTARIO.md)

#### Formato del QR
Los códigos QR deben tener el formato: `REFERENCIA|SERIAL`

Ejemplo: `OG971390|202630010002`

### Escanear un Código QR

1. **Iniciar escáner**: Haz clic en el botón "Iniciar Escaneo"
2. **Permitir cámara**: Acepta los permisos cuando el navegador los solicite
3. *Ver Registros Recientes

- El panel derecho muestra los últimos 20 escaneos
- Información completa: ID, Referencia, Serial, Estado, Fechas
- Actualización automática cada 30 segundos
- Códigos de color por estado (azul=almacén, verde=despachado)
- Badge verde: 🚚 DESPACHADO

#### Escaneos Posteriores
- ⚠️ Muestra advertencia "Ya despachado"
- No modifica los datos existenteste
5. **Confirmación visual**: Verás un mensaje de éxito y el resultado

### Cambiar de Cámara

- Usa el selector "Seleccionar cámara..." para cambiar entre cámaras
- Útil en dispositivos cinventario-qr-YYYY-MM-DD.csv`
4. Compatible con Excel, Google Sheets, etc.

### Estadísticas

- **Total escaneados**: Contador total de productos únicos
- **Hoy**: Operaciones realizadas en el día actual
- **En Almacén**: Productos actualmente en inventario (📦)
- **Despachados**: Productos ya despachados (🚚
### Ver Registros Recientes

- El panel derecho muestra los últimos 20 escaneos
- Información completa: ID, tipo, contenido, fecha, hora, dispositivo
- Actualización automática cada 30 segundos

### Exportar Datos

1. Haz clic en el botón "Exportar"
2. Se descargará un archivo CSV con todos los registros
3. Nombre del archivo: `qr-scans-YYYY-MM-DD.csv`
4. Compatible con Excel, Google Sheets, etc.

### Estadísticas

- **Total escaneados**: Contador total de QR guardados
- **Hoy**: Cantidad escaneada en el día actual
- **Por tipo**: Distribución porcentual (URL, Email, Texto, etc.)

## 🔒 Seguridad y Buenas Prácticas

### Implementadas en el Código

✅ **Validación de datos**: Verificación antes de guardar en Sheets
✅ **Variables de entorno**: Credenciales nunca en el código
✅ **CORS configurado**: Protección contra peticiones no autorizadas
✅ **Manejo de errores**: Try-catch en todas las operaciones críticas
✅ **Sanitización**: Limpieza de datos del usuario
✅ **HTTPS recomendado**: Para producción (ver sección despliegue)

### Recomendaciones Adicionales

🔐 **Nunca subas a Git**:
- Archivo `.env`
- Archivo JSON de credenciales
- Carpeta `node_modules/`

🛡️ **En producción**:
- Usa HTTPS obligatorio
- Configura límites de rate-limiting
- Implementa autenticación de usuarios si es necesario
- Monitorea logs y errores

📝 **Buenas prácticas**:
- Realiza backups regulares de la hoja de cálculo
- Revisa los permisos de la service account
- Mantén las dependencias actualizadas: `npm audit`

## 🌐 Despliegue en Producción

### Opción 1: Render.com (Gratis)

1. Crea cuenta en [Render.com](https://render.com)
2. Conecta tu repositorio Git
3. Crea un nuevo **Web Service**
4. Configura:
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
5. Agrega variables de entorno desde el panel
6. ¡Deploy automático!

### Opción 2: Heroku

```bash
# Instalar Heroku CLI
heroku login
heroku create qr-scanner-app

# Configurar variables de entorno
heroku config:set GOOGLE_SPREADSHEET_ID=tu_id
heroku config:set GOOGLE_CLIENT_EMAIL=tu_email
heroku config:set GOOGLE_PRIVATE_KEY="tu_clave"

# Deploy
git push heroku main
```

### Opción 3: Railway.app

1. Ve a [Railway.app](https://railway.app)
2. Conecta tu GitHub
3. Selecciona el repositorio
4. Agrega variables de entorno
5. Deploy automático en cada push

### Opción 4: VPS (DigitalOcean, AWS, etc.)

```bash
# En el servidor
git clone <tu-repo>
cd qr-scanner-app
npm install
npm install -g pm2

# Configurar .env
nano .env

# Iniciar con PM2
pm2 start server.js --name qr-scanner
pm2 startup
pm2 save

# Configurar Nginx como reverse proxy
# y SSL con Let's Encrypt
```

### Configurar HTTPS

Para producción, HTTPS es **esencial**:

**Con Nginx + Let's Encrypt:**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d tudominio.com
```

**Con servicios cloud (Render, Heroku):**
- SSL incluido automáticamente

## 🧪 Testing y Debugging

### Verificar Salud del Servidor

```bash
# En navegador o con curl
curl http://localhost:3000/api/health
```

Respuesta esperada:
```json
{
  "status": "OK",
  "message": "Servidor funcionando correctamente",
  "timestamp": "2026-01-30T..."
}
```

### Probar Guardado Manual

```bash
curl -X POST http://localhost:3000/api/save-qr \
  -H "Content-Type: application/json" \
  -d '{
    "qrContent": "https://example.com",
    "browserInfo": {
      "browser": "Chrome",
      "os": "Windows",
      "device": "Desktop"
    }
  }'
```
Formato de QR inválido"**
- ✅ Verifica que el QR tenga el formato: `REFERENCIA|SERIAL`
- ✅ Asegúrate de usar el símbolo pipe (`|`)
- ✅ No incluyas espacios adicionales

**Error: "
### Ver Logs del Servidor

```bash
# Si usas PM2
pm2 logs qr-scanner

# O con node directamente
node server.js
```

### Problemas Comunes

**Error: "Error al conectar con Google Sheets"**
- ✅ Verifica que el `GOOGLE_SPREADSHEET_ID` sea correcto
- ✅ Confirma que compartiste la hoja con el service account
- ✅ Revisa que `GOOGLE_PRIVATE_KEY` tenga los `\n` correctos

**Error: "No se detectaron cámaras"**
- ✅ Otorga permisos de cámara en el navegador
- ✅ Verifica que otra app no esté usando la cámara
- ✅ En producción, requiere HTTPS

**Error: "CORS policy"** (sistema de inventario).

**Body:**
```json
{
  "qrContent": "OG971390|202630010002"
}
```

**Respuesta (Primer escaneo):**
```json
{
  "success": true,
  "action": "stored",
  "message": "Producto registrado EN ALMACEN",
  "data": {
    "id": 42,
    "referencia": "OG971390",
    "serial": "202630010002",
    "estado": "EN ALMACEN",
    "fechaAlmacen": "30/01/2026"
  }
}
```

**Respuesta (Segundo escaneo):**
```json
{
  "success": true,
  "action": "dispatched",
  "message": "Producto marcado como DESPACHADO",
  "data": {
    "id": 42,
    "referencia": "OG971390",
    "serial": "202630010002",
    "estado": "DESPACHADO",
    "fechaAlmacen": "30/01/2026",
    "fechaDespacho": "31/01/2026
  "browserInfo": {
    "browser": "Chrome",
    "os": "Windows",
    "device": "Desktop"
  },referencia": "OG971390",
      "serial": "202630010002",
      "estado": "DESPACHADO",
      "fechaAlmacen": "30/01/2026",
      "fechaDespacho": "31/01/2026",
      "horaAlmacen": "14:30:00",
      "horaDespacho": "16:45:00
{
  "success": true,
  "isDuplicate": false,
  "message": "QR guardado exitosamente",
  "data": {
    "id": 42,
    "type": "URL",
    "timestamp": "2026-01-30T12:00:00.000Z"
  }
}
```

### GET `/api/recent-scans?limit=10`
Obtiene los últimos N registros.

**ParenAlmacen": 45,
    "despachados": 105,
    "today": 12
  "success": true,
  "data": [
    {
      "id": "42",
      "content": "https://example.com",
      "type": "URL",
      "date": "30/01/2026",
      "time": "12:00:00",
      "browser": "Chrome",
      "os": "Windows",
      "device": "Desktop"
    }
  ]
}
```

### GET `/api/stats`
Obtiene estadísticas de escaneos.

**Respuesta:**
```json
{
  "success": true,
  "data": {
    "total": 150,
    "today": 12,
    "byType": {
      "URL": 80,
      "Texto": 45,
      "Numérico": 25
    }
  }
}
```

## 🎨 Personalización

###REFERENCIA',
  'SERIAL',
  'ESTADO',
  'FECHA_ALMACEN',
  'FECHA_DESPACHO',
  'HORA_ALMACEN',
  'HORA_DESPACHO',
  'Tu Nuevo Campo'  // ← Agregar aquí
]);
```

### Cambiar Estados del Sistema

Si necesitas más estados además de EN ALMACEN y DESPACHADO, modifica la lógica en [server.js](server.js) en la ruta `/api/save-qr`.
```

### Modificar Límite de Registros Recientes

En [public/app.js](public/app.js):

```javascript
// Cambiar de 20 a tu valor preferido
const response = await fetch(`${API_URL}/api/recent-scans?limit=20`);
```

### Agregar Nuevos Campos a la Hoja

En [server.js](server.js), modifica `initializeSheet()`:

```javascript
await sheet.setHeaderRow([
  'ID',
  'Contenido QR',
  'Tipo',
  'Fecha',
  'Hora',
  'Navegador',
  'Sistema Operativo',
  'Dispositivo',
  'Tu Nuevo Campo'  // ← Agregar aquí
]);
```

## 🤝 Contribuciones

¡Las contribuciones son bienvenidas! Si encuentras bugs o tienes ideas para mejorar:

1. Fork el proyecto
2. Crea una rama: `git checkout -b feature/nueva-funcionalidad`
3. Commit: `git commit -m 'Agregar nueva funcionalidad'`
4. Push: `git push origin feature/nueva-funcionalidad`
5. Abre un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Consulta el archivo `LICENSE` para más detalles.

## 👨‍💻 Soporte

Si necesitas ayuda:
- 📧 Abre un issue en GitHub
- 📚 Revisa la documentación de [html5-qrcode](https://github.com/mebjas/html5-qrcode)
- 📖 Consulta la [documentación de Google Sheets API](https://developers.google.com/sheets/api)

## 🙏 Créditos

- **html5-qrcode**: Librería de escaneo QR por [mebjas](https://github.com/mebjas/html5-qrcode)
- **Google Sheets API**: Por Google
- **Express**: Framework web por [TJ Holowaychuk](https://github.com/expressjs/express)

---

**Desarrollado con ❤️ para facilitar el escaneo y gestión de códigos QR**

¡Feliz escaneo! 📱✨
