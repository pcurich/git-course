---
title: "Git Branch"
weight: 15
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1 ¿Qué es una rama (branch)?**

Una rama es un puntero móvil que representa una línea de desarrollo dentro de tu proyecto. Permite trabajar en paralelo, experimentar funcionalidades o aislar correcciones sin alterar la rama principal (main).

| Término     | Descripción                                                              |
|-------------|---------------------------------------------------------------------------|
| `main`      | Rama principal del proyecto.                                              |
| `HEAD`      | Puntero que indica el commit actual, normalmente sobre una rama.         |
| `branch`    | Puntero a un commit, que avanza a medida que se hacen nuevos commits.    |

## **2 Crear y gestionar ramas (local)**

```tpl
# Crear una nueva rama local
git branch nombre-rama

# Crear y cambiar a la nueva rama directamente
git checkout -b nombre-rama
```

| Acción                          | Comando                          |
|----------------------------------|----------------------------------|
| Crear rama local                | `git branch nueva-rama`          |
| Crear y cambiar a nueva rama   | `git checkout -b nueva-rama`     |
| Listar ramas locales            | `git branch`                     |
| Ver rama actual                 | `git branch --show-current`      |

## **3 Gestión de ramas remotas**

```tpl
# Subir rama al repositorio remoto
git push origin nombre-rama

# Ver todas las ramas remotas
git branch -r

# Ver ramas locales y remotas
git branch -a
```

| Acción                    | Comando                            |
|---------------------------|-------------------------------------|
| Subir rama                | `git push origin nombre-rama`      |
| Ver ramas remotas         | `git branch -r`                    |
| Ver ramas locales+remotas | `git branch -a`                    |

## **4 Cambio entre ramas (checkout)**

git checkout permite cambiar el contenido del área de trabajo. Su uso más común es cambiar de rama, pero también puede:

- Mover HEAD a un commit específico
- Restaurar archivos desde otro commit
- Salir o entrar en estado detached HEAD

```tpl
# Cambia a la rama main
git checkout main

# Crea y cambia a una nueva rama dev
git checkout -b dev

# Ver historial en esa rama
git log --oneline
```

| Acción             | Comando                   |
|--------------------|---------------------------|
| Cambiar de rama    | `git checkout rama`       |
| Cambiar y crear    | `git checkout -b nueva`   |
| Restaurar archivo  | `git checkout rama -- archivo.txt` |
| Cambiar a un commit (detached HEAD)| `git checkout a1b2c3d`|
| Restaura el archivo desde el último commit actual| `git checkout HEAD archivo.txt`|
| Extrae style.css desde la rama main al directorio actual| `git checkout main -- style.css`|

### 4.1 Crear Rama y Checkout

Representa el flujo base y la bifurcación de featureX desde master
{{< mermaid >}}
graph LR
    A1[commit A] --> B1[commit B] --> C1[commit C] --> D1[commit D]
    C1 --> FX1["featureX: nuevo puntero"]
    subgraph Ramas
        MA1(master)
        FX1(featureX)
    end
    classDef master fill:#f9f,stroke:#333,stroke-width:2px
    class MA1 master
{{< /mermaid >}}

```bash
git branch featureX
git branch
# * master
#   featureX
```

HEAD cambia a la rama featureX

{{< mermaid >}}
graph LR
    A2[commit A] --> B2[commit B] --> C2[commit C] --> D2[commit D]
    C2 --> FX2[featureX]
    subgraph HEAD actual
        H2(HEAD → featureX)
    end
    H2 --> FX2
    classDef active fill:#cfc,stroke:#333,stroke-width:2px
    class H2,FX2 active
{{< /mermaid >}}

```bash
git checkout featureX
git branch
#   master
# * featureX
```

| Comando                 | Acción que representa         | Nodo clave |
|------------------------|-------------------------------|------------|
| `git branch featureX`  | Crea rama desde HEAD          | C          |
| `git checkout featureX`| Mueve HEAD a featureX         | FX         |

## **5 Eliminar ramas (local y remoto)**

```tpl
# Borrar rama local (solo si ya fue fusionada)
git branch -d nombre

# Forzar borrado de rama local
git branch -D nombre

# Borrar rama remota
git push origin --delete nombre
```

| Tipo    | Comando                            | Uso                           |
|---------|-------------------------------------|--------------------------------|
| Local   | `git branch -d nombre`              | Borrado seguro (fusionada)    |
| Local   | `git branch -D nombre`              | Borrado forzado               |
| Remoto  | `git push origin --delete nombre`   | Elimina rama en servidor      |

## **6 Casos comunes**

### 6.1 Caso 1: Borrado tras merge exitos

**Situación**: Has completado la rama `feature/navbar` y la has fusionado con `main`.

```bash
git branch -d feature/navbar
```

### 6.2 Caso 2: Rama obsoleta sin fusionar

**Situación**: Se creó `feature/old-config`, pero nunca se integró ni se volverá a usar.

```bash
git branch -D feature/old-config
```

### 6.3 Caso 3: Borrar rama remota tras integración

**Situación**: Has fusionado `hotfix/login-error` en `main` y subido todo al remoto.

```bash
git push origin --delete hotfix/login-error

# Complementa con git fetch --prune si se quiere limpiar referencias remotas localmente.
```

### 6.4 Caso 4: Intento de eliminar rama activa

**Situación**: Estás situado en `feature/invoice`. Intentas borrarla y Git lanza error.

```bash
git checkout main
git branch -d feature/invoice

# recordar el uso de HEAD y cómo Git protege la rama activa
```

### 6.5 Caso 5: Limpieza mensual de ramas ya fusionadas

**Situación**: Quieres eliminar todas las ramas locales que ya se fusionaron con `main`.

```bash
git branch --merged main | grep -v '\*' | xargs git branch -d
```

## **7 ¿Qué es un dangling commit?**

Un dangling commit es un commit que no tiene referencias vivas apuntando a él: ninguna rama, tag ni HEAD. Existen en el repositorio, pero están “huérfanos”. Pueden generarse por errores, rebases incompletos o eliminación de ramas sin merge.

### 7.1 Escenario 1: Eliminar una rama sin hacer merge

**Situación**: Se trabajó en `feature/test`, se hicieron varios commits, pero luego la rama se elimina sin merge.
git branch -D feature/test

**Resultado**: Los commits sobreviven temporalmente como dangling, pero no son accesibles por comandos normales.
Reforzamiento CLI:

```bash
git fsck --lost-found
Git no borra los datos inmediatamente, sino que los aparta.
```

### 7.2 Escenario 2: Rebase interrumpido

**Situación**: Un rebase falla a mitad de camino y los commits originales quedan sin referencias.

```bash
git rebase -i HEAD~3
# cancelas el rebase a medias
```

**Resultado**: Los commits anteriores pueden quedar como dangling si no se recuperan.

### 7.3 Escenario 3: Reset duro

Situación: Se hace un hard reset a un commit anterior, eliminando commits recientes.

```bash
git reset --hard HEAD~2

# considerar a diferenciar --soft, --mixed, y --hard, y sus efectos en el historial
```

**Resultado**: Los dos commits descartados quedan sin referencias → dangling.
🧠 Genial para enseñar .

## **8 El registro secreto de Git**

`git reflog` muestra el historial completo de movimientos de HEAD, incluyendo acciones que no aparecen en `git log`, como rebases, resets, checkouts, merges fallidos y cambios temporales.
Es ideal para recuperar commits perdidos, visualizar el flujo reciente del repositorio y diagnosticar errores complejos.

```bash
# Muestra los cambios recientes en HEAD y otros punteros locales
git reflog

# Reflog de la rama específica featureX
git reflog show featureX

# Revierte el estado del repositorio al punto 2 pasos atrás según el reflog
git reset --hard HEAD@{2}
```

### 8.1 Ejemplo típico

```bash
git commit -m "Cambio experimental"
git reset --hard HEAD~1
git reflog
```

Aunque el commit “Cambio experimental” desaparece del log, ¡aún puedes verlo y restaurarlo con reflog!

```bash
git checkout HEAD@{1}
git branch restaurada
```

### 8.2 Resumen de usos de `git reflog`

| Caso de uso                     | Comando                         | Resultado                     |
|-------------------------------|----------------------------------|-------------------------------|
| Ver cambios recientes de HEAD | `git reflog`                    | Lista numerada de acciones   |
| Revertir a estado anterior     | `git reset --hard HEAD@{n}`     | Estado del repo retrocedido  |
| Recuperar commit huérfano      | `git checkout HEAD@{n}`         | Permite crear nueva rama     |
