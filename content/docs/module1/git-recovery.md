---
title: "Git Recovery"
weight: 16
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1. Gestión de Commits y Recuperación en Git**

Comprender cómo se crean, modifican y revierten commits en Git, y cómo los comandos `reset`, `restore` y `checkout` interactúan con las tres áreas clave del sistema de control de versiones.

### 1.1 Git Locations

| Área | Alias común | Descripción breve |
|-|-|-|
| Working Directory | WD | Archivos locales editables |
| Staging Area | Index | Archivos preparados para el próximo commit |
| Local Repository | HEAD | Historial confirmado de commits |

### 1.2 Comandos Claves

#### `git commit -m "mensaje"`

Registra los cambios del Staging Area en el Local Reposito

#### `git reset`

Reescribe el historial y/o staging

```bash
git reset [--soft | --mixed | --hard] <commit>
```

| Opción | Afecta a... | Descripción didáctica |
| - | - | - |
| --soft | HEAD | Mueve el puntero del commit, pero conserva el staging y los archivos |
| --mixed | HEAD + Staging Area | Mueve el puntero y borra el staging, pero conserva los archivos |
| --hard | HEAD + Staging + Working Dir. | Borra todo: historial, staging y archivos locales |
| (sin opción) | Equivale a --mixed | Comportamiento por defecto |

##### Reset --soft

- Solo mueve el puntero del commit. El staging y los archivos permanecen intactos.

{{< mermaid >}}
flowchart LR
    E[Local Repository] -->|git reset --soft HEAD^| C[Staging Area]
    C --> D[git commit -m 'nuevo mensaje']
{{< /mermaid >}}

##### Reset --mixed (por defecto)

- El staging se borra, pero los archivos siguen en el WD.

{{< mermaid >}}
flowchart LR
    E[Local Repository] -->|git reset HEAD^| A[Working Directory]
{{< /mermaid >}}

##### Reset --hard

- Todo se borra: commit, staging y archivos locales.

{{< mermaid >}}
flowchart LR
    E[Local Repository] -->|git reset --hard HEAD^| A[Working Directory]
{{< /mermaid >}}

#### `git restore`

- Recupera archivos desde el repo (`HEAD`) o staging hacia el Working Directory.

```bash
git restore archivo.txt
git restore --staged archivo.txt
```

##### Restore desde staging o repo

{{< mermaid >}}
flowchart LR
    C[Staging Area] -->|git restore --staged archivo| A[Working Directory]
    E[Local Repository] -->|git restore archivo| A
{{< /mermaid >}}

#### `git checkout`

Cambia de rama o recupera versione

##### Checkout para cambiar de rama o recuperar archivo

{{< mermaid >}}
flowchart LR
    E[Local Repository] -->|git checkout rama| A[Working Directory]
    E -->|git checkout HEAD archivo| A
{{< /mermaid >}}

### 1.3 Errores comunes y soluciones

| Error | Causa probable | Solución sugerida |
|-|-|-|
| nothing to commit | No hay cambios en staging | Usa git add antes de commit |
| fatal: pathspec did not match | Archivo no existe en esa ubicación | Verifica nombre y ubicación |
| detached HEAD | Usaste checkout sin rama | Usa git switch -c nueva-rama |
| overwritten changes | Usaste reset --hard sin respaldo | Usa git reflog para intentar recuperar |

## **2. Preparación del entorno de prueba**

```bash
# Crear un nuevo repositorio de prueba
mkdir reset-demo && cd reset-demo
git init

# Crear archivo y hacer primer commit
echo "Primera línea" > archivo.txt
git add archivo.txt
git commit -m "Commit 1: archivo inicial"

# Modificar archivo y hacer segundo commit
echo "Segunda línea" >> archivo.txt
git add archivo.txt
git commit -m "Commit 2: agrega segunda línea"

# Modificar archivo y hacer tercer commit
echo "Tercera línea" >> archivo.txt
git add archivo.txt
git commit -m "Commit 3: agrega tercera línea"

# Ver historial
git log --oneline
```

### 1. `git reset --soft <commit>`

```bash
# Volver al segundo commit, conservando staging y archivos
git reset --soft HEAD~1

# Ver estado
git status
git log --oneline
```

**Resultado:**

- El commit 3 desaparece del historial.
- Los cambios siguen en staging listos para un nuevo commit.

### 2. `git reset --mixed <commit> (por defecto)`

```bash
# Volver al segundo commit, borrando staging pero conservando archivos
git reset HEAD~1

# Ver estado
git status
git log --oneline
```

**Resultado:**

- El commit 3 desaparece.
- Los cambios están en el working directory pero no en staging.

### 3. `git reset --hard <commit>`

```bash
# Volver al segundo commit, borrando todo (staging y archivos)
git reset --hard HEAD~1

# Ver estado
git status
git log --oneline
```

**Resultado:**

- El commit 3 desaparece.
- Los cambios se eliminan del disco.
