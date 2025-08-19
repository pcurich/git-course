---
title: "Git Diff"
weight: 17
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1. Exploración y Comparación de Cambios en Git**

### 1.1. Definiciones generales

| Comando    | Descripción                                                 |
|-|-|
| `git diff`   | Muestra diferencias entre archivos, commits o el staging    |
| `git show`   | Muestra detalles de un commit específico                    |
| `git log`    | Muestra el historial de commits                             |

## **2. `git diff`**

`git diff` compara contenido entre distintas Git locations:

- Working Directory vs Staging Area
- Staging Area vs Local Repository
- Entre commits específico

{{< mermaid >}}
flowchart LR
    WD["📂 Working Directory"]
    SA["📥 Staging Area (Index)"]
    LR["📦 Local Repository (HEAD)"]
    WD -->|git diff| SA
    SA -->|git diff --cached| LR
{{< /mermaid >}}

### 2.1 Ejemplos de uso

```bash
# Cambios no preparados (WD vs Staging)
git diff

# Cambios preparados (Staging vs Repo)
git diff --cached

# Comparar dos commits
git diff HEAD~1 HEAD

# Mostrar solo nombres de archivos modificados
git diff --name-only

# Mostrar diferencias palabra por palabra
git diff --word-diff
```

### 2.2 Ejercicio

- Modifique un archivo sin hacer `add`, luego ejecuta `git diff`.
- Hacer `git add`, luego ejecuta `git diff --cached`.
- Compara dos commits con `git diff HEAD~2 HEAD`

## **3. `git show`**

`git show` muestra el contenido y metadatos de un commit específico

### 3.1 Ejemplos de uso

```bash
# Mostrar último commit
git show

# Mostrar commit específico
git show abc1234

# Mostrar solo nombres de archivos modificados
git show --name-only abc1234

# Mostrar diferencias palabra por palabra
git show --word-diff abc1234
```

### 3.2  Ejercicio

- Ejecuta `git log --oneline` y copia un hash.
- Usa `git show <hash>` para ver el contenido del commit.
- Usa `--name-only` y `--word-diff` para ver variantes

## **4. `git log`**

`git log` muestra el historial de commits. Puede filtrarse y formatearse

### 4.1 Ejemplos de uso

```bash
# Historial completo
git log

# Versión compacta
git log --oneline

# Mostrar cambios con cada commit
git log -p

# Mostrar solo nombres de archivos modificados
git log --name-only

# Mostrar diferencias palabra por palabra
git log --word-diff
```

### 4.2  Ejercicio

- Ejecuta `git log --oneline` y observa los identificadores.
- Usa `git log -p` para ver los cambios línea por línea.
- Usa `--name-only` y `--word-diff` para comparar salidas

## **5. Tabla comparativa de opciones**

| Comando          | Opción                        | Descripción                   |
|-|-|-|
| git diff         | `--cached`                      | Compara staging vs repo       |
| git diff         | `--name-only`                   | Muestra solo nombres          |
| git diff         | `--word-diff`                   | Muestra cambios por palabra   |
| git show         | `<commit>`                      | Muestra contenido del commit  |
| git show         | `--name-only`                   | Archivos modificados          |
| git show         | `--word-diff`                   | Cambios por palabra           |
| git log          | `--oneline`                     | Historial compacto            |
| git log          | `-p`                            | Historial con diferencias     |
| git log          | `--name-only`                   | Archivos por commit           |
| git log          | `--word-diff`                   | Cambios por palabra           |
