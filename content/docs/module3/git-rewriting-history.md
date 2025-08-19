---
title: "Git Rewriting History"
weight: 35
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1 Rewriting History en Git**

Reescribir la historia en Git permite modificar commits existentes, ya sea para corregir errores, pulir el historial, simplificar la integración, o preparar el proyecto para revisión o publicación. Esta sección cubre:

- `git commit --amend`
- `git rebase -i` (interactivo)
- Squash merge (`--squash`)
- Commits seleccionados (`cherry-pick`)

### 1.1 Amending

#### ¿Qué es Amend?

`git commit --amend` permite modificar el último commit sin crear uno nuevo. Puede usarse para:

- Agregar archivos olvidados (-a)
- Corregir el mensaje
- Editar el contenido (manteniendo o cambiando el mensaje)

#### Ejemplo de Amend

```bash
# Olvidaste agregar un archivo
git add archivo-extra.md

# Reemplazas el commit anterior incluyendo el archivo
git commit --amend -a
```

Si deseas mantener el mensaje sin editarlo:

```bash
git commit --amend --no-edit
```

Esto genera un nuevo commit con nuevo ID SHA. Ejemplo:

```bash
git log --oneline

Anterior: abc123 Fix typos
Actual:   def456 Fix typos   ← nuevo ID


Antes:
A---B---C   ← HEAD

Después:
A---B---C'  ← C fue reemplazado por C'
```

### 1.2 Cherry-pick

#### ¿Qué es Cherry-pick?

`git cherry-pick <commit>` aplica un commit específico desde otra rama sin reescribir o fusionar el resto del historial. Es útil para:

- Recuperar un commit puntual
- Integrar cambios específicos sin traer toda la rama
- Resolver integración puntual entre ramas paralelas

#### Ejemplo de Cherry-pick

```bash
# Desde main, aplicar un commit de featureX
git checkout main
git cherry-pick abc123


# También puedes aplicar varios:
git cherry-pick abc123 def456

Rama featureX:    A---B---C
Rama main:        D---E

Cherry-picked:    D---E---C'   ← solo C es traído como nuevo commit
```

El commit C' es una copia de C, con nuevo SHA e independiente del historial original.

### 1.3 Interactive Rebase (git rebase -i)

#### ¿Qué permite?

Modificar múltiples commits con control granular. Opciones disponibles:

| Acción | Descripción |
|-|-|
| pick | Usar el commit tal cual |
| reword | Editar el mensaje del commit |
| edit | Detener y modificar contenido del commit |
| drop | Eliminar el commit |
| squash | Fusionar con el anterior (mantiene mensaje combinado) |
| fixup | Fusionar sin conservar mensaje (silencioso) |
| exec | Ejecutar comandos Shell durante el rebase |
| Reordenar | Cambiar el orden de los commits |

#### Cuándo usarlo

- Limpiar historial antes de enviar a remoto
- Preparar presentación del trabajo
- Agrupar o eliminar commits irrelevantes

#### Ejemplo de Rebase interactivo

```bash
git rebase -i HEAD~4

# Abre el editor:
pick abc123 Agrega login
pick def456 Corrige error ortográfico
pick ghi789 Refactoriza validación
pick jkl012 Elimina consola

# Modificable:
reword    → Cambiar mensaje
edit      → Detener y modificar contenido
squash    → Fusionar con anterior (mantiene mensajes)
fixup     → Fusionar sin mensaje
drop      → Eliminar el commit
exec echo → Ejecuta comando Shell

Original:
A---B---C---D

Interactivo con squash y drop:
A---B+C---D'     (C fue fusionado, D modificado)
```

### 1.4 Squash Merge

#### ¿Qué es un Squash Merge?

`git merge --squash` fusiona los cambios de una rama sin conservar sus commits individuales. Se usa para:

- Integrar múltiples commits como uno solo
- Mantener historia limpia en ramas principales

#### Ejemplo de Squash Merge

```bash
# Estás en main y quieres fusionar feature
git checkout main
git merge --squash feature
git commit -m "Feature integrada en un solo commit"

# Los commits de feature no se muestran como parte del historial; solo el resultado final aparece.
Antes:
main:    A---B
feature:     C---D---E

Después del squash merge:
main:    A---B---F   ← F = resultado consolidado
```

### 1.5 Rewriting History: ¿Cuándo usar qué?

| Técnica | Uso principal | Riesgos o cuidados |
|-|-|-|
| `amend` | Corregir el último commit | Reescribe historia; evita si ya se hizo push |
| `rebase -i` | Limpiar, reordenar, eliminar commits | Reescribe múltiples commits |
| `--squash` | Fusionar múltiples commits como uno solo | No conserva el historial original |
| `cherry-pick` | Tomar commits puntuales de otra rama | Duplica commits (nuevo SHA) |

#### ¿Cuándo y por qué reescribir?

| Situación | Herramienta sugerida |
|-|-|
| Olvidaste incluir un archivo | `git commit --amend` |
| Quieres modificar varios commits | `git rebase -i` |
| Deseas fusionar todos los commits de una rama | `git merge --squash` |
| Necesitas extraer un commit puntual | `git cherry-pick <SHA>` |

#### Comparación entre técnicas

| Técnica | ¿Reescribe SHA? | ¿Afecta historial? | Uso ideal |
|-|-|-|-|
| `amend` | ✅ Sí | ✅ Último commit | Corrige último commit |
| `rebase -i` | ✅ Sí | ✅ Varios commits | Limpiar o reestructurar historial |
| `merge --squash` | ✅ Sí (nuevo) | ❌ Preserva main | Integrar cambios sin comprometer historial fuente |
| `cherry-pick` | ✅ Sí | ❌ Crea duplicado | Extraer commits específicos |
