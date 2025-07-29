---
title: "Git Tracking Branch"
weight: 32
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1 ¿Qué es una tracking branch?**

Una tracking branch es una rama local que está vinculada a una rama remota. De forma local el nombre de una rama de rastreo comienza con el nombre remoto, luego una barra diagonal y despues el nombre de la rama (`<remote>/<branch>`). Su función principal es hacer seguimiento de los cambios en el remoto, facilitando comandos como:

```bash
git pull        # Trae cambios del remoto a tu rama local
git push        # Envía tus commits al remoto correspondiente
```

Se crean automáticamente al clonar un repo o manualmente con opciones como `--set-upstream`.

{{< mermaid >}}
flowchart LR
  subgraph Local_Repo
    C[Commit A] --> A["main (local)"]
    D[Commit B] --> A
  end

  subgraph Remote_Repo
    F[Commit C] --> E[origin/main]
    G[Commit D] --> E
  end

  %% tracking relationship (visual punteado)
  A -. tracking branch .-> E

  %% flujo conceptual de sincronización
  A --> X[git push]
  E --> Y[git fetch/pull]

{{</ mermaid >}}

## **2 ¿Qué es `HEAD` en Git?**

`HEAD` es un puntero que indica tu posición actual en el historial de commits. En una situación normal, `HEAD` apunta a la rama activa (por ejemplo, `master`), y esa rama a su vez apunta a su último commit.

```bash
# Ver el puntero actual
git rev-parse HEAD     # Muestra el hash del commit actual
git symbolic-ref HEAD  # Muestra a qué rama está apuntando HEAD (ej: refs/heads/master)
```

### 2.1 `HEAD` y las tracking branches

Cuando trabajas con una rama local que sigue a una remota (origin/master), el flujo se ve así:

```tpl
HEAD → master → Commit X
       ↑
       tracking: origin/master → Commit Y
```

#### ¿Qué implica esto?

- Si origin/master tiene commits nuevos que no están en master, Git te avisará que tu rama local está `behind`.
- Si tu master local tiene commits que no están en origin/master, Git dirá que estás `ahead`.
- Esto se puede verificar con

```bash
git status         # Indicará si estás ahead/behind
git fetch          # Trae datos actualizados del remoto
git branch -vv     # Muestra el tracking y la distancia con el remoto
```

#### Ejemplo

Supongamos que estás en master, que está vinculado a origin/master.

```bash
# HEAD apunta a master
HEAD → master

# master sigue a origin/master
master ──▶ origin/master
```

Ahora haces un nuevo commit en `master` local:

```bash
echo "Nuevo contenido" > archivo.txt
git add archivo.txt
git commit -m "Cambios locales"
```

El commit existe en master, pero aún no en origin/master. Si ejecutas:

```bash
git status
```

Verás algo como:

```bash
Your branch is ahead of 'origin/master' by 1 commit.
```

Esto indica que HEAD está en una rama local que tiene cambios no sincronizados con su rama remota tracking

### 2.2 Resumen: Estados de HEAD en relación con la rama remota

| Estado local     | `HEAD` apunta a... | Relación con rama remota (`origin/<branch>`) | Acción sugerida               |
|------------------|---------------------|----------------------------------------------|-------------------------------|
| Synced           | Último commit de la rama local  | Mismo commit que rama remota                | Ninguna, estás al día         |
| Ahead            | Commit más nuevo que el remoto  | Local tiene commits que el remoto no tiene  | `git push` para sincronizar   |
| Behind           | Commit más antiguo que el remoto| Remoto tiene commits nuevos                 | `git pull` para actualizar    |
| Diverged         | Commit exclusivo en ambos lados | Cada uno tiene commits no compartidos       | `git pull --rebase` o merge   |

## 3 **Visualización de nombres de tracking branches**

Cuando trabajas con ramas que rastrean remotas (tracking branches), es útil entender cómo visualizar esa relación desde distintos comandos

### 3.1 Comandos clave

| Comando | ¿Para qué sirve? |
|------------------|---------------------|
| git branch | Lista ramas locales |
| git branch --all | Lista locales + remotas (remotes/origin/...) |
| git branch -vv | Muestra qué rama local sigue a qué remota, y si está ahead/behind |
| git log origin/master --oneline | Ver historial remoto sin cambiar de rama |
| git symbolic-ref HEAD | Ver qué rama está apuntando HEAD (ej: refs/heads/master) |
| git remote set-head origin `<branch>` | Cambia la rama por defecto del remoto (ej. develop) |

### 3.2 Ejemplo CLI: visualización y cambio del HEAD remoto

```bash
# Ver todas las ramas (locales + remotas)
git branch --all

# Salida esperada
# * main
#   remotes/origin/HEAD -> origin/master
#   remotes/origin/main
#   remotes/origin/develop

# Ver a qué rama apunta HEAD del remoto
git symbolic-ref refs/remotes/origin/HEAD
# remotes/origin/master

# Cambiar HEAD remoto a 'develop'
git remote set-head origin develop

# Confirmar cambio
git symbolic-ref refs/remotes/origin/HEAD
# remotes/origin/develop
```

{{< mermaid >}}
flowchart LR
  subgraph Local
    A[main]
    B[develop]
  end
  subgraph Remote [origin]
    C[origin/main]
    D[origin/develop]
    E["origin/HEAD ➜ origin/develop"]
  end

  A -. tracks .-> C
  B -. tracks .-> D
{{</ mermaid >}}

ramas locales y su relación con remota

## 4 **Visualización del estado de seguimiento (tracking status)**

### 4.1 Comandos clave

| Comando | Función |
|------------------|---------------------|
| git status | Muestra si la rama local está ahead, behind, o up to date |
| git fetch | Actualiza la información remota sin alterar tu historial local |
| git log --all --oneline --graph | Visualiza commits con relación entre ramas, ideal para entender divergencias |

### 4.12 Ejemplo CLI: rama main con commits nuevos (estado ahead)

```bash
# Ver estado actual
git status

# Salida esperada
# On branch main
# Your branch is ahead of 'origin/main' by 2 commits.

# Visualización gráfica del historial
git log --all --oneline --graph

# Ejemplo de salida:
# * 9e3a7d2 (main) Fix typo in docs
# * b2c61e4 Add helper module
# | * 5d2f5b1 (origin/main) Initial commit
# |/
```

{{< mermaid >}}
gitGraph
   commit id: "Commit remoto" tag: "origin/main"
   commit id: "Commit local 1"
   commit id: "Commit local 2" tag: "HEAD ➜ main"
{{</ mermaid >}}
