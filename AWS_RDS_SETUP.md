# 🚀 Guía: Transición a AWS RDS

## Resumen de los cambios realizados

✅ **Script Python (`init_database.py`)**: Automatiza toda la inicialización de la BD
✅ **Rutas relativas en `set_up.sql`**: Compatible con cualquier entorno
✅ **`db_connection.py`**: Ya está configurado para AWS RDS

---

## Paso a paso: Inicializar base de datos en AWS RDS

### 1️⃣ Pre-requisitos

- **AWS RDS MySQL disponible** con acceso desde tu IP
- **Python 3.8+** instalado
- **Dependencias instaladas**:
  ```bash
  pip install mysql-connector-python python-dotenv
  ```

### 2️⃣ Verificar credenciales en `.env`

Asegúrate que el archivo `.env` tenga las credenciales correctas de AWS RDS:

```env
DB_HOST=base-de-datos-inacap.cn6ygo0o8ng0.us-east-2.rds.amazonaws.com
DB_PORT=3306
DB_NAME=inacap_test
DB_USER=root
DB_PASSWORD=Pinki_y_negra1
```

> **⚠️ Nota de seguridad**: Mantén este archivo en `.gitignore` (ya configurado)

### 3️⃣ Probar conexión (OPCIONAL pero recomendado)

Antes de inicializar, verifica que la conexión funciona:

```bash
python test_db_connection.py
```

**Salida esperada:**
```
✓ Conectado a: root@base-de-datos-inacap.cn6ygo0o8ng0.us-east-2.rds.amazonaws.com:3306/inacap_test
✓ Base de datos actual: inacap_test
✓ Tablas en la BD: X
```

### 4️⃣ Inicializar base de datos en AWS

Ejecuta el script de inicialización:

```bash
python init_database.py
```

**Salida esperada:**
```
======================================================================
INICIALIZADOR DE BASE DE DATOS - AWS RDS / MySQL Local
======================================================================

📋 Configuración de conexión:
  Host: base-de-datos-inacap.cn6ygo0o8ng0.us-east-2.rds.amazonaws.com
  Port: 3306
  User: root
  Database: inacap_test

🔗 Conectando a base de datos...
✓ Conexión exitosa!

📊 Creando base de datos...
✓ Base de datos 'inacap_test' creada

📝 Ejecutando scripts SQL:

✓ Tabla: estudiante
✓ Tabla: semestre
✓ Tabla: asignatura
... (más tablas)
✓ Semilla: estudiante_asignatura

======================================================================
✅ INICIALIZACIÓN COMPLETADA CON ÉXITO

📊 Estadísticas:
  Base de datos: inacap_test
  Tablas creadas: 8
  Charset: utf8mb4
  Collation: utf8mb4_unicode_ci
======================================================================
```

---

## Solución de problemas

### Error: "Access denied for user 'root'"
```
❌ Posibles causas:
1. Credenciales en .env incorrectas
2. Security Group de AWS RDS no permite tu IP
3. Usuario 'root' no existe en RDS

✓ Soluciones:
- Verifica credenciales en AWS RDS Console
- Ve a RDS > Databaases > Security Groups
- Agrega tu IP: Edit Inbound Rules > Add Rule (MySQL/Aurora, port 3306, your IP)
```

### Error: "Connection refused"
```
❌ Posibles causas:
1. AWS RDS no está disponible
2. Host en .env es incorrecto
3. Puerto 3306 bloqueado

✓ Soluciones:
- Verifica que RDS está en estado "available"
- Copia el endpoint correctamente desde AWS Console
- Intenta: telnet <host> 3306
```

### Error: "Source file not found"
```
❌ Posibles causas:
1. Archivos SQL no están en las rutas esperadas
2. Ejecutaste script desde directorio incorrecto

✓ Soluciones:
- Ejecuta desde el directorio raíz del proyecto
- Verifica estructura de carpetas: database/schema/ y database/seed_data/
```

---

## Métodos alternativos

### Opción 2: Ejecutar manualmente con MySQL CLI

Si prefieres ejecutar SQL directamente a RDS:

```bash
# 1. Conectarse a RDS
mysql -h base-de-datos-inacap.cn6ygo0o8ng0.us-east-2.rds.amazonaws.com \
      -u root -p

# 2. En el prompt de MySQL:
DROP DATABASE IF EXISTS inacap_test;
CREATE DATABASE inacap_test CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE inacap_test;

# 3. Ejecutar archivos (desde el cliente MySQL):
SOURCE /ruta/absolute/database/schema/core/estudiante.sql;
SOURCE /ruta/absolute/database/schema/core/semestre.sql;
... (y así sucesivamente)
```

### Opción 3: Usar MySQL Workbench

1. Abre MySQL Workbench
2. Crea nueva conexión con los datos de `.env`
3. Ejecuta manualmente `database/set_up.sql` (con rutas relativas actualizadas)

---

## ✅ Verificar que todo funcionó

```bash
python test_db_connection.py
```

Debería mostrar:
- ✓ Conexión exitosa
- ✓ Base de datos actual: inacap_test
- ✓ Tablas en la BD: 8+ (según tu estructura)

---

## Próximos pasos

1. **Actualiza tu aplicación** para usar `DatabaseConnection`
2. **Configura CI/CD** para auto-inicializar BD en deployments
3. **Configura backups automáticos** en AWS RDS
4. **Cambia la contraseña** `root` por una más segura

---

## Preguntas frecuentes

**P: ¿Puedo reutilizar la BD existente en AWS?**
A: Sí, `init_database.py` hace `DROP DATABASE` primero. Si quieres preservar datos, comenta esa línea.

**P: ¿Funciona con BD locales también?**
A: Sí, solo actualiza `.env` con `DB_HOST=localhost`

**P: ¿Puedo ejecutar el script desde CI/CD (GitHub Actions, etc)?**
A: Sí, solo asegúrate que `.env` esté disponible en el ambiente (via secrets)

---

## Resumen de archivos modificados

| Archivo | Cambio |
|---------|--------|
| `database/set_up.sql` | Actualizar rutas de hardcodeadas a relativas |
| `init_database.py` | ✨ NUEVO - Script Python para inicializar BD |
| `.env` | Ya configurado con AWS RDS (no tocar) |
| `db_connection.py` | Sin cambios (ya está listo para AWS) |

