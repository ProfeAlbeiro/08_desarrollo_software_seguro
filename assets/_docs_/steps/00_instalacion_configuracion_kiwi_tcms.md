# Paso 0: Instalación y Configuración de Kiwi TCMS

## Objetivo: 

Tener Kiwi TCMS corriendo localmente accesible vía https://localhost, con un usuario administrador creado.

## Requisitos previos

Docker Desktop instalado. Docker Compose ya viene incluido con Docker Desktop, no requiere instalación aparte

---

## 0.1. Instalar Docker Desktop

1. Verificar requisitos del sistema:

- Windows 10/11 de 64 bits (Professional, Enterprise, Education o Home)
- Virtualización habilitada en la BIOS
- Se recomienda WSL 2 para mejor rendimiento

2. Descargar e instalar:

- Visita el sitio oficial de Docker: https://www.docker.com/products/docker-desktop y descarga el instalador para Windows.
- También puedes instalarlo vía PowerShell con winget:


```powershell
winget install Docker.DockerDesktop
```

3. Iniciar Docker Desktop:

- Después de la instalación, busca "Docker Desktop" en el menú inicio y ejecútalo. Acepta los términos de servicio. Espera a que el ícono de la ballena en la bandeja del sistema deje de animarse (indica que Docker está listo).

4. Verificar en PowerShell de VS Code:

```powershell
docker --version
docker compose version
```

- Ambos deben devolver información de versión. Docker Compose usa la sintaxis docker compose (con espacio), no docker-compose.


## 0.2. Crear directorio y descargar docker-compose.yml

Kiwi TCMS no proporciona imágenes versionadas en Docker Hub, pero el archivo docker-compose.yml oficial está disponible en GitHub .

Repositorio oficial de Kiwi TCMS en GitHub: https://github.com/kiwitcms/Kiwi

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


## 0.3. 


## 0.4. 


## 0.5. 


## 0.6. 