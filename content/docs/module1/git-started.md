---
title: "Git Started"
weight: 11
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1. Instalación y Verificación de Versión**

Git es una herramienta de control de versiones distribuido. Para comenzar, se debe instalar Git en el sistema operativo correspondiente.

```bash
# En sistemas Debian/Ubuntu
sudo apt update
sudo apt install git

# En macOS con Homebrew
brew install git

# En Windows
# Usar el instalador desde: https://git-scm.com/download/win
```

Verificar instalación

```bash
git --version

#resultado
git version 2.38.1.windows.1
```

## **2. Sintaxis General de Git**

La sintaxis de Git sigue esta estructura:

```cpl
git [comando] [--flags] [argumentos]
```

Git utiliza convenciones para representar opciones, argumentos y valores:

| Símbolo        | Significado                                  | Ejemplo                                     |
|-|-|-|
| `--flag`         | Opción larga                                 | `git config --global user.name "Pedro"`       |
| `-f`             | Opción corta                                 | `git commit -m "mensaje"`                     |
| `<valor>`        | Argumento obligatorio                        | `git checkout <branch>`                       |
| `[<valor>]`      | Argumento opcional                           | `git log [<archivo>]`                         |
| `()`           | Agrupación o precedencia                     | `git log --pretty=(oneline\|short)`        |
| \|              | Alternativa entre opciones                   | `git config --local \| --global`             |
| ...            | Repetición o múltiples valores               | `git add archivo1 archivo2 archivo3 ...`      |

## **3. Obtener Ayuda**

Git incluye documentación integrada accesible desde la línea de comandos.

```bash
git config -h
```

## **4. Configuración de Usuario y Editor**

Git permite configurar datos del usuario y preferencias como el editor de texto. Las configuraciones pueden aplicarse a nivel:

| Nivel     | Alcance                    | Ubicación del archivo de configuración           |
|-|-|-|
| `--system`  | Todo el sistema               |`C:\ProgramData\Git\config`|
| `--global`  | Usuario actual                |`C:\Users\Usuario1\.gitconfig`|
| `--local`   | Repositorio actual            |`D:\Proyectos\MiRepo\.git\config`|

```bash
# Configuración de Git con config
git config [--local | --global | --system] <clave> [<valor>] 

# Configurar nombre y correo
git config --global user.name "Usuario1"
git config --global user.email "Usuario1@example.com"

# Configurar editor por defecto
git config --global core.editor ["nano" | "code --wait"]

# Mejora las salidas de color en el editor
git config --global color.ui true

# Al hacer checkout: Convierte automáticamente los finales de línea LF (Linux/Mac) a CRLF (Windows)
# Al hacer commit: Convierte automáticamente los finales de línea CRLF (Windows) a LF (Linux/Mac)
git config --global core.autocrlf true

# Establece el identificador del commit (hash) a los primeros 7 caracteres
git config --global core.abbrev 7
```

Ver configuración actual

```bash
# Ver toda la configuración (combina system + global + local)
git config --list

# Ver toda la configuración mostrando de dónde viene cada valor
git config --list --show-origin

# Ver toda la configuración con el alcance (system/global/local)
git config --list --show-scope

# Ver configuración del sistema (--system)
git config --system --list

# Ver configuración global del usuario (--global)
git config --global --list

# Ver configuración local del repositorio (--local)
git config --local --list
```
