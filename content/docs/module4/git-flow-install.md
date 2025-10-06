---
title: "Git Flow Install"
weight: 42
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## Instalación y configuración

### 2.1 Cómo instalar Git Flow en distintos sistemas operativos

Git Flow es una extensión que se instala sobre Git. Aquí te muestro cómo hacerlo en los sistemas más comunes:

**En Linux (Debian/Ubuntu):**

```bash
sudo apt update
sudo apt install git-flow
```

**En macOS (usando Homebrew):**

```bash
brew install git-flow-avh
```

💡 Nota: La versión recomendada es la de AVH Edition, que incluye mejoras y mantenimiento activo.

**En Windows**
Puedes instalarlo desde Git for Windows SDK o usar herramientas gráficas como Sourcetree, que ya lo incluyen.
También puedes usar Git Bash y seguir los pasos de instalación de Linux.

### 2.2 Inicialización del flujo en un repositorio (git flow init)

Una vez instalado, se debe inicializar Git Flow en cada repositorio donde se quiera usar.

#### Paso a paso

1 Crea o entra a tu repositorio:

```bash
git init
cd mi-proyecto
```

2 Ejecuta:

```bash
git flow init
```

3 El sistema te pedirá confirmar los nombres de las ramas. Puedes aceptar los valores por defecto:

```bash
Branch name for production releases: master
Branch name for "next release" development: develop
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []
```

Esto crea automáticamente las ramas master y develop si no existen.

### 2.3 Configuración recomendada para equipos

Para que Git Flow funcione bien en entornos colaborativos, se recomienda:
**Establecer convenciones de nombres:**

- Usar prefijos consistentes (`feature/`, `release/`, `hotfix/`)
- Evitar espacios o caracteres especiales en nombres de ramas

**Documentar el flujo de trabajo:**

- Crear un archivo `README.md` o `CONTRIBUTING.md` con reglas como:
- Cómo nombrar ramas
- Cuándo iniciar un `release` o `hotfix`
- Cómo hacer revisiones (Pull Requests, Merge Requests)

**Integrar con herramientas de CI/CD:**

- Configurar pipelines para que se ejecuten al fusionar `release` o `hotfix` a `master`
- Automatizar despliegues desde `master`

**Roles claros en el equipo:**

- Desarrolladores: trabajan en `feature`
- QA/Testers: validan en `release`
- Líder técnico: aprueba y fusiona `hotfix` y `release`
