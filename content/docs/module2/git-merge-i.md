---
title: "Git Merge I"
weight: 16
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1 ¿Qué es un merge en Git?**

Un merge en Git es una operación que integra el historial de dos ramas distintas en una sola. Esto permite unir cambios realizados en flujos paralelos de trabajo, manteniendo la coherencia del proyecto.

Internamente, Git compara los historiales de las ramas implicadas para encontrar su antecesor común (conocido como base del merge), y luego aplica los cambios divergentes.

## **2 ¿Cómo afecta al DAG?**

Git no trabaja con archivos directamente, sino con un grafo acíclico dirigido (DAG) de commits. Cada merge altera esta estructura:

- En un merge commit, se crea un nodo con dos padres: uno de la rama actual y otro de la rama fusionada.
- En un fast-forward, el puntero simplemente avanza sin modificar la topología del DAG.

**¿Cuándo ocurre un merge?**

| Situación | Ejemplo técnico |
|-----------|-----------------|
| Finalizar una rama de desarrollo | `git merge feature/login` |
| Unificar cambios tras resolución de conflictos | `git merge bugfix/menu` |
| Colaboración multiusuario con ramas divergentes | `git merge otra_rama` |

## **3 Tipos de merge en Git**

### **3.1 Fast-forward**

Es una estrategia de fusión en Git donde la rama base no ha avanzado desde que se bifurcó la rama secundaria. En este caso, Git simplemente mueve el puntero de la rama base al último commit de la rama secundaria, sin crear un nuevo commit de merge.
No modifica la estructura del DAG, y se percibe como una extensión lineal del historial

#### Antes vs. después en el DAG

##### Situación antes del merge

{{< mermaid >}}
gitGraph
    commit id:"A" tag:"main"
    commit id:"B"
    branch featureX
    checkout featureX
    commit id:"C"
{{</ mermaid>}}

- main apunta a A.
- featureX contiene nuevos commits (B, C) que no existen en main.

##### Después del merge fast-forward

{{< mermaid >}}
gitGraph
    commit id:"A"
    commit id:"B"
    commit id:"C" tag:"main"
{{</ mermaid>}}

- main avanza hasta C (el mismo nodo que featureX).
- No se genera nuevo commit de merge.
- Se conserva historia limpia y lineal.

#### Comparativa: Local vs. Remoto

##### Repositorio Local

- Ideal para flujos individuales o ramas que no sufrieron interferencia.
- Ejemplo:

```bash
git checkout main
git merge featureX
```

- Si main no cambió desde el nacimiento de featureX, el merge será fast-forward

##### Repositorio Remoto

- Si otros contribuyeron a `main`, Git **no permite fast-forward** automáticamente.
- En Pull Requests, se suele generar **merge commit** para preservar historial de colaboración.

```bash
git fetch origin
git checkout main
git merge --ff-only origin/featureX
```

`--ff-only` garantiza que solo se ejecuta el merge si es fast-forward.

#### Escenarios comunes de uso

| Contexto                          | Estrategia recomendada |
|----------------------------------|------------------------|
| Desarrollo aislado               | Fast-forward           |
| Hotfix rápido                    | Fast-forward           |
| Feature terminada sin conflictos | Fast-forward           |
| Revisión de cambios entre equipos| Merge commit           |
| Pull Request en GitHub           | Merge commit (por defecto) |

#### Buenas prácticas

Usar fast-forward cuando:

- Se desea un historial limpio y lineal.
- No hubo interferencias con la rama base (`main`).
- El trabajo fue exclusivo de una rama y puede integrarse sin perder contexto.

Evitar fast-forward cuando:

- Se quiere conservar la separación lógica del trabajo (ej. colaboraciones).
- Se desea tener trazabilidad de integración por revisiones o QA.
- Se usa un modelo como Git Flow, donde cada integración tiene importancia documental.

#### Ejemplo

```bash
# flujo local con fast-forward
git checkout featureX
git commit -m "Desarrollo terminado"
git checkout main
git merge featureX  # merge rápido, sin conflictos

# visualizar el DAG (simplificado)
git log --oneline --graph --all
```

### **3.2 Merge commit**

Un merge commit es un tipo de fusión en Git que genera un nuevo commit con dos padres, unificando dos historiales divergentes. Es usado cuando ambas ramas han progresado desde el punto de bifurcación y no puede aplicarse una estrategia fast-forward.

Este tipo de merge:

- Preserva la historia completa de ambas ramas.
- Mantiene la topología del DAG acíclico.
- Crea un punto claro de integración para auditoría, revisión y navegación.

#### Antes y Después de ejecutar el merge

##### Situación antes del merge (historial divergente)

{{< mermaid >}}
gitGraph
    commit id:"A"
    commit id:"B"
    branch featureX
    checkout featureX
    commit id:"C"
    checkout main
    commit id:"D"
{{</ mermaid>}}

- featureX tiene commit C exclusivo.
- main avanzó hasta D, separándose del punto de bifurcación B.

##### Después del merge commiT

{{< mermaid >}}
gitGraph
    commit id:"A"
    commit id:"B"
    branch featureX
    checkout featureX
    commit id:"C"
    checkout main
    commit id:"D"
    merge featureX tag:"Merge commit"
{{</ mermaid>}}

#### Ejemplos prácticos: local vs remotO

##### En repositorio local

```bash
git checkout main
git merge featureX
```

- Si main y featureX tienen commits distintos → se genera merge commit.
- Puedes verificar con

```bash
git log --graph --oneline
```

##### En repositorio remoto

Opción: Merge commit por defecto en Pull Request
Al aceptar un PR, GitHub genera un merge commit por defecto (a menos que elijas squash o rebase). Ejemplo visual:

```bash
*   Merge pull request #42 from featureX
|\
| * commit C
* | commit D
```

Si se hace merge local y luego push

```bash
git merge featureX  # crea merge commit
git push origin main  # sube DAG completo al remoto
```

#### Escenario de uso

| Escenario | ¿Por qué usar merge commit? |
|-----------|-----------------------------|
| Pull Requests entre ramas divergentes | Auditoría clara, contexto colaborativo |
| Integración de hotfix urgente mientras se desarrolla un feature | Separación lógica del flujo |
| Revisión de ramas por QA antes de despliegue | Permite rastrear qué fue revisado y cuándo |
| Implementaciones por múltiples desarrolladores | Documenta el momento exacto de integración |
| Uso de Git Flow u otros flujos estructurados | Cada fase tiene su propio commit de merge documentado |

#### Buenas prácticas al usar merge commit

- Escribe mensajes claros en los commits de merge. Ejemplo:

```bash
git merge featureX -m "Merge 'featureX': añade soporte a OAuth"
```

- Evita conflictos innecesarios sincronizando tu rama base:

```bash
git checkout main
git pull origin main
```

- No borres ramas antes del merge. Preserva el historial para auditar.
- Documenta fusiones importantes en la historia del proyecto, especialmente si están ligadas a releases, QA o decisiones de negocio.

## **4 Visualizaciones del concepto de merge**

**Diagrama 1**: Merge commit (historia bifurcada + unificación)
{{< mermaid >}}
gitGraph
    commit id:"A"
    commit id:"B"
    branch featureX
    checkout featureX
    commit id:"C"
    checkout main
    commit id:"D"
    merge featureX tag:"Merge commit"
{{< /mermaid >}}

El commit final tiene dos padres: uno de main, otro de featureX.

**Diagrama 2**: Fast-forward (avance lineal del puntero)
{{< mermaid >}}
gitGraph
    commit id:"A"
    commit id:"B"
    branch featureY
    checkout featureY
    commit id:"C"
    checkout main
    merge featureY

{{< /mermaid >}}

main simplemente avanza al commit C. No hay nuevos nodos.

## **4 Tabla resumen**

| Tipo de merge      | Crea commit nuevo | Conserva historia de ambas ramas | Uso recomendado           |
|--------------------|------------------|----------------------------------|---------------------------|
| Fast-forward       | No               | Parcial                          | Proyectos lineales        |
| Merge commit       | Sí               | Completa                         | Equipos colaborativos     |
