# 0. Instalación y Configuración de Kiwi TCMS en Windows

**Objetivo:** Tener Kiwi TCMS corriendo localmente, accesible vía https://localhost, con un usuario administrador creado.  
**Requisito previo:** Docker Desktop instalado con WSL2 habilitado.  
**Repositorio oficial de Kiwi TCMS:** https://github.com/kiwitcms/Kiwi

## 0.1 Instalar Docker Desktop

1. **Verificar requisitos del sistema:**
   - Windows 10/11 de 64 bits (Professional, Enterprise, Education o Home)
   - Virtualización habilitada en la BIOS
   - WSL2 habilitado (recomendado para mejor rendimiento)

2. **Habilitar WSL2 (si no está habilitado):**  
   Abre PowerShell como administrador y ejecuta:
```powershell
wsl --install
```
   Reinicia el equipo después de la instalación .

3. **Descargar e instalar Docker Desktop:**  
   Visita el sitio oficial: https://www.docker.com/products/docker-desktop y descarga el instalador para Windows. Ejecútalo y sigue las instrucciones .

4. **Iniciar Docker Desktop:**  
   Busca "Docker Desktop" en el menú inicio y ejecútalo. Acepta los términos de servicio. Espera a que el ícono de la ballena en la bandeja del sistema deje de animarse.

5. **Verificar en PowerShell de VS Code:**
```powershell
docker --version
docker compose version
```
   Ambos deben devolver información de versión. Docker Compose usa la sintaxis `docker compose` (con espacio), no `docker-compose`.

## 0.2 Crear directorio y descargar docker-compose.yml

En PowerShell de VS Code:
```powershell
# Crear directorio de trabajo
mkdir ~/kiwi-tcms
cd ~/kiwi-tcms

# Descargar docker-compose.yml
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/kiwitcms/Kiwi/master/docker-compose.yml" -OutFile "docker-compose.yml"
```
Verificar que se descargó:
```powershell
Get-Content docker-compose.yml -Head 20
```

## 0.3 Levantar los contenedores

```powershell
docker compose up -d
```
Esto descargará las imágenes y creará dos contenedores:
- **kiwi_web:** la aplicación Kiwi TCMS
- **kiwi_db:** la base de datos MariaDB

También crea dos volúmenes persistentes: `kiwi_db_data` y `kiwi_uploads` .  
Verificar que están corriendo:
```powershell
docker compose ps
```
Ambos deben mostrar estado Up.

### Solución al error del paso 0.3

Error que experimentaste:
```text
Container reverse_proxy Starting
Error response from daemon: failed to create task for container: ...
error mounting ".../tests/nginx-proxy/nginx.conf" to rootfs at "/etc/nginx/nginx.conf": 
not a directory: Are you trying to mount a directory onto a file (or vice-versa)?
```

**Causa:** El archivo `docker-compose.yml` oficial incluye un servicio llamado `reverse_proxy` que intenta montar un archivo `nginx.conf` desde la ruta local `tests/nginx-proxy/nginx.conf`. Como no descargaste ese archivo, Docker creó automáticamente un directorio con ese nombre. En el siguiente intento, Docker detecta que está intentando montar un directorio donde debería haber un archivo, y lanza este error .

**Solución (Opción recomendada): Eliminar el servicio reverse_proxy**  
Este servicio es una configuración opcional para desarrollo de Kiwi TCMS, no es necesario para usar la herramienta normalmente.

Abre el archivo `docker-compose.yml` con VS Code o Notepad:
```powershell
code docker-compose.yml
```

Busca el bloque que comienza con `reverse_proxy:` y elimínalo completo (todo el servicio, desde `reverse_proxy:` hasta el siguiente servicio o el final del archivo).

Guarda el archivo y ejecuta:
```powershell
# Limpiar contenedores anteriores
docker compose down

# Reiniciar
docker compose up -d
```

**Solución alternativa (si prefieres mantener la configuración original):**  
Si deseas conservar el servicio `reverse_proxy`, necesitas crear manualmente el archivo que falta:
```powershell
# Crear el directorio y archivo
mkdir tests\nginx-proxy
New-Item -Path "tests\nginx-proxy\nginx.conf" -ItemType File -Force

# Luego reiniciar
docker compose up -d
```

## 0.4 Configuración inicial

**Importante:** Este paso es obligatorio antes de acceder por el navegador .
```powershell
docker exec -it kiwi_web /Kiwi/manage.py initial_setup
```

**Problema común en PowerShell:** Si el comando falla con error de ruta, usa una de estas alternativas:
```powershell
# Opción A: Usar WSL
wsl docker exec -it kiwi_web /Kiwi/manage.py initial_setup

# Opción B: Usar cmd
cmd /c "docker exec -it kiwi_web /Kiwi/manage.py initial_setup"

# Opción C: Escapar la ruta
docker exec -it kiwi_web //Kiwi//manage.py initial_setup
```

**Interacción esperada:**
```text
Username: admin
Email address: admin@localhost
Password: ********
Password (again): ********
Domain name: localhost
```

Este comando crea la estructura de la base de datos (aplica migraciones), crea el superusuario, configura el dominio y ajusta permisos internos .  
Verificar que las migraciones se aplicaron:
```powershell
docker exec -it kiwi_web /Kiwi/manage.py showmigrations
```
Todas deben mostrar `[X]` (aplicadas).

## 0.5 Acceder a la interfaz web

Abre el navegador y ve a:
```text
https://localhost
```
**Nota:** Es https con "s". Kiwi TCMS usa un certificado autofirmado, por lo que el navegador mostrará una advertencia de seguridad. Esto es normal .

- **En Chrome/Edge:** Haz clic en "Avanzado" → "Continuar a localhost (no seguro)"
- **En Firefox:** Haz clic en "Avanzado..." → "Aceptar el riesgo y continuar"

Inicia sesión con el usuario y contraseña que creaste en el paso 0.4.

## 0.6 Verificar instalación

Si puedes ver la página de inicio de Kiwi TCMS con las opciones **Test Plan**, **Test Cases**, **Test Runs** en la barra de navegación, el Paso 0 está completo al 100%.

---

## Comandos útiles de referencia

| Acción | Comando |
| :--- | :--- |
| **Ver estado** | `docker compose ps` |
| **Ver logs** | `docker compose logs -f web` |
| **Detener** | `docker compose down` |
| **Reiniciar** | `docker compose up -d` |
| **Config inicial** | `docker exec -it kiwi_web /Kiwi/manage.py initial_setup` |

---

## Enlace del repositorio de Kiwi TCMS

- **GitHub oficial:** https://github.com/kiwitcms/Kiwi
- **Documentación oficial de instalación con Docker:** https://kiwitcms.readthedocs.io/en/stable/installing_docker.html