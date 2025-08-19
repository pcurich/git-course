---
title: "Git Graph Model"
weight: 14

# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1. El modelo de grafo en Git**

Entender cómo Git representa el historial de cambios como un grafo dirigido acíclico (DAG), y cómo cada commit se conecta con sus antecesores.

## **2. DAG visual**

{{< mermaid >}}
graph LR
    A[Commit A] --> B[Commit B]
    B --> C[Commit C]
    C --> D[Commit D]
    B --> E[Feature Branch: Commit E]
    E --> F[Commit F]
    F --> G[Merge Commit G]
    D --> G
    classDef commit fill:#e0f7fa,stroke:#00796b,stroke-width:2px,rx:10;
    class A,B,C,D,E,F,G commit;
{{< /mermaid >}}

## **3. Comandos esenciales para entender el DAG en Git**

| 🛠 Comando                             | 📘 Propósito                                                             | 🧾 Explicación                                                                 |
|--------------------------------------|-------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| `git log --oneline --graph --decorate --all` | Visualizar el grafo completo de commits                                 | Dibuja el DAG con líneas de conexión, nombres de ramas y commits compactos.   |
| `git show <commit>`                  | Inspeccionar un nodo específico                                         | Muestra los detalles, cambios y metadatos de un commit individual.            |
| `git diff <commit1> <commit2>`      | Comparar dos nodos del grafo                                            | Permite ver los cambios entre dos commits, útil para estudiar su evolución.   |
| `git reflog`                         | Consultar el historial interno del HEAD                                 | Muestra los movimientos recientes, ideal para recuperar commits perdidos.     |
| `git cat-file -p <commit>`          | Ver el contenido crudo de un commit                                     | Revela cómo Git almacena los objetos internamente en el grafo.                |
| `git rev-parse <ref>`               | Obtener el SHA-1 de una referencia                                      | Identifica un nodo del grafo con su hash único.                               |
| `git fsck`                           | Verificar la integridad del DAG                                         | Comprueba que no existan objetos corruptos o referencias rotas.               |

{{< mermaid >}}
graph LR
  A[git log] --> DAG[🔗 DAG]
  B[git show commit] --> DAG
  C[git diff commit1 commit2] --> DAG
  D[git reflog] --> DAG
  E[git cat-file -p commit] --> DAG
  F[git rev-parse ref] --> DAG
  G[git fsck] --> DAG

  DAG --> H[Historia de commits]
  DAG --> I[Relaciones entre nodos]
  DAG --> J[Estructura interna]

  classDef cmd fill:#e0f7fa,stroke:#00796b,stroke-width:2px,rx:10;
  class A,B,C,D,E,F,G cmd;

{{< /mermaid >}}

{{< hint info >}}
Este gráfico representa cómo diversos comandos de Git permiten explorar, inspeccionar y verificar el DAG (grafo dirigido acíclico) que modela la historia del proyecto. Cada nodo representa un commit, y las relaciones muestran cómo fluye el desarrollo del código.
{{< /hint >}}

## **Ejercicio: Explorando el DAG en Git usando comandos**

A continuación, se presenta una simulación guiada por consola para construir y explorar el DAG (grafo dirigido acíclico) en Git. Cada paso está comentado para entender su propósito dentro del modelo.

{{< details title="Comandos" open=true >}}

{{< highlight powershell >}}
:: ✅ Inicializar un repositorio vacío
git init demo-dag
cd demo-dag

:: ✅ Crear el primer archivo y hacer el commit inicial
echo "Inicio del proyecto" > index.txt
git add index.txt
git commit -m "🔨 Commit inicial"

:: ✅ Crear una rama de desarrollo
git branch develop
git checkout develop

:: ✅ Añadir una función en develop
echo "Función A implementada" >> index.txt
git commit -am "🧩 Añadir función A"

:: ✅ Crear rama de feature y añadir login
git checkout -b feature/login
echo "Login básico" >> index.txt
git commit -am "🔐 Crear login básico"

:: ✅ Visualizar el grafo actual
git log --oneline --graph --decorate --all
:: Muestra commits conectados como nodos del DAG, junto con ramas y decoraciones

:: ✅ Fusionar rama de login en develop
git checkout develop
git merge feature/login -m "🔗 Fusionar login en develop"

:: ✅ Comparar commits
git diff HEAD~1 HEAD
:: Muestra diferencias entre el último y penúltimo commit

:: ✅ Inspeccionar un commit específico
git show HEAD
:: Muestra detalles, cambios y metadatos del commit actual

:: ✅ Ver movimientos del HEAD y hashes
git reflog
git rev-parse HEAD
:: Permite rastrear navegación y obtener hash único de HEAD

:: ✅ Examinar contenido interno del commit
git cat-file -p HEAD
:: Visualiza cómo Git representa el commit como nodo del DAG

:: ✅ Verificar integridad del repositorio
git fsck
:: Chequea que no haya corrupción en el grafo
{{< /highlight >}}

{{< /details >}}

{{< details title="Gráfico" open=false >}}
    {{< mermaid >}}
        gitGraph
            commit id: "🔨 Commit inicial"
            branch develop
            commit id: "🧩 Función A (develop)"
            branch feature/login
            commit id: "🔐 Login básico (feature/login)"
            checkout develop
            merge feature/login tag: "🔗 Merge login -> develop"
            commit id: "🛠 Ajustes post-merge (develop)"
            checkout main
            merge develop tag: "🚀 Merge develop -> main"

    {{< /mermaid >}}
{{< /details >}}
