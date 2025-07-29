---
title: "Git Rebase"
weight: 34
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1 Rebasing en Git: Reescribiendo el historial con propósito**

Rebasing consiste en reaplicar commits de una rama sobre otra, como si se hubieran creado a partir de una base diferente. Se utiliza para mantener el historial limpio y lineal.

### 1.1 Formas de ejecutar un `rebase`

```bash
# 1. Rebase simple (desde HEAD actual)
git rebase <upstream>

# 2. Rebase explícito entre dos ramas
git rebase <upstream> <topic-branch>
```

¿Qué hace cada uno?

| Comando | Acción |
|-|-|
| git rebase main | Reaplica tus commits locales sobre main (desde la rama actual) |
| git rebase main feature | Reaplica feature sobre main, sin cambiar de rama |

### 1.2 Ejecución básica del rebase

```bash
# Estás en la rama feature
git checkout feature

# Reaplicas los commits de feature sobre main
git rebase main
```

{{< hint info >}}
Esto mueve los commits únicos de feature para que partan desde la punta de main.
{{</ hint  >}}

```tpl
Antes del rebase:

main:     A---B---C
feature:            D---E

Después del rebase:

main:     A---B---C
feature:              D'--E'
```

{{< hint info >}}
Los commits D y E se reescriben como D' y E', simulando que se crearon después de C.
{{</ hint  >}}

### 1.3 Tipos de Rebase

| Tipo | Características | Uso recomendado |
|-|-|-|
|  Rebase regular | Reaplica commits automáticamente, sin intervención manual | Flujo simple y rápido |
|  Rebase interactivo (-i) | Permite editar, combinar (squash), eliminar (drop) y reordenar commits | Preparación antes de integración (presentación, limpieza de historial) |

Ejemplo interactivo

```bash
git rebase -i main
```

Y el editor muestra:

```bash
pick abc123 Añade login básico
pick def456 Refactoriza validaciones
pick ghi789 Corrige errores tipográficos
```

Puedes reemplazar pick por:

- `reword`: cambia mensaje
- `squash`: fusiona con anterior
- `drop`: elimina el commit

### 1.4 Rebasing con conflictos de merge

Cuando los commits que se van a reaplicar modifican los mismos archivos que la base, surgen conflictos:

```bash
git rebase main
# ❌ CONFLICT (content): Merge conflict in index.html

# Resolución paso a paso
# Editas manualmente el archivo
# Luego marcas como resuelto:
git add index.html

# Continúas el rebase:
git rebase --continue


# O puedes abortar:
git rebase --abort
```

### 1.5 Simulación CLI: rebasing con conflicto

> 1 Crear repositorio de prueba.

```bash
mkdir repo-rebase-conflicto
cd repo-rebase-conflicto
git init
echo "Línea A" > archivo.txt
git add archivo.txt
git commit -m "Commit A en main"
```

> 2 Crear una rama y hacer cambios divergentes.

```bash
git checkout -b feature
echo "Línea B desde feature" >> archivo.txt
git commit -am "Commit B en feature"
```

> 3 Volver a main y crear conflicto

```bash
git checkout main
echo "Línea B desde main (conflicto)" >> archivo.txt
git commit -am "Commit C en main"
```

> 4 Intentar hacer rebase

```bash
git checkout feature
git rebase main
```

🔴 Git detecta conflicto:

```bash
CONFLICT (content): Merge conflict in archivo.txt
```

🧭 Resolución del conflicto

```bash
# Ver el contenido del archivo con conflicto
cat archivo.txt

# Editar y resolver manualmente
# Eliminar los marcadores <<< === >>>

git add archivo.txt
git rebase --continue
```

📊 Visualización con `git log --graph --oneline`

```bash
# Antes del rebase (divergente):
git log --graph --oneline

* bbb111 Commit B en feature
| * aaa000 Commit C en main
|/
* 123abc Commit A en main

# Después del rebase exitoso (conflicto resuelto):
* 999xyz Commit B en feature (reaplicado)
* aaa000 Commit C en main
* 123abc Commit A en main
```

{{< hint info >}}
Commit B se ha reaplicado después de C, como parte del nuevo historial lineal.
{{</ hint  >}}

### 1.6 Reaplicar commits: verificar cambios con `git diff`

Durante el rebase, puedes usar:

```bash
git diff HEAD FETCH_HEAD
```

O si estás en medio de un conflicto:

```bash
git diff
```

Esto te permite:

- Ver qué está cambiando respecto al nuevo punto de base
- Comparar el contenido antes de reaplicar (commit actual) con la rama destino (main, por ejemplo)

### 1.6 Rebasing vs Merging: Pros y Contras

| Estrategia | ✅ Pros | ❌ Contras |
|-|-|
| merge | Historial completo con contexto de integración | Crea commits extra, puede ensuciar el historial |
| rebase | Historial lineal y limpio, ideal para revisiones | Reescribe commits, puede confundir si hay colaboradores |

### 1.7 ¿Cuándo usar cada uno?

- Usa merge cuando quieres conservar el momento de integración entre ramas (ej. equipos múltiples).
- Usa rebase cuando quieres una historia limpia antes de compartir o presentar.

#### Flujo de `git merge` con conflicto

```tpl
MERGE:
       A
      / \
     B   C
      \ /
       D  <-- merge con posible conflicto
```

```bash
git checkout master
git merge featureX          # ⛔ Conflicto
git status                  # ✅ Muestra "both modified: fileA.txt"
# Editar y resolver manualmente conflicto en fileA.txt
git add fileA.txt
git commit                  # ✅ Confirma merge con resolución
```

#### Flujo de `git rebase` con conflicto

```tpl
REBASE:
    A -> B -> C'
           ↑
     reaplicación del commit de featureX
```

```bash
git checkout featureX
git rebase master           # ⛔ Conflicto
git status                  # ✅ Muestra "both modified: fileA.txt"
# Editar y resolver manualmente conflicto en fileA.txt
git add fileA.txt
git rebase --continue       # ✅ Continúa el rebase
```
