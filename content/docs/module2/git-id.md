---
title: "Git ID"
weight: 13
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1 ¿Qué es un Git ID?**

- Un Git ID es el nombre de un objeto git
- Cada commit en Git tiene un identificador único: un hash SHA-1 de 40 caracteres.
- Este ID garantiza la integridad, unicidad y trazabilidad de cada cambio.
- Git usa estos IDs para comparar, referenciar y verificar commits.

## **2 Tipos de Git Objects**

Git almacena todo como objetos en **#`.git/objects`#**. Hay cuatro tipos principales:

|Tipo de objeto |Contenido                  |Función principal                                   |Comando para inspección                  |
|------------------|------------------------------|--------------------------------------------------------|---------------------------------------------|
| **Blob**         | Contenido de archivos        | Almacena el contenido puro de un archivo               | `git cat-file -p <hash>`                    |
| **Tree**         | Estructura de directorios    | Relaciona blobs y otros trees para formar jerarquías   | `git cat-file -p <tree_hash>`               |
| **Commit**       | Snapshot + metadatos         | Registra el estado del proyecto y sus padres           | `git show <commit_hash>`                    |
| **Tag**          | Referencia anotada           | Marca versiones importantes con metadatos opcionales   | `git cat-file -p <tag_hash>`                |

## 3 **Comandos para visualizar objetos Git**

| Comando                          | Propósito                                 | Ejemplo de uso                          |
|-----------------------------------|----------------------------------------------|--------------------------------------------|
| `git cat-file -t <hash>`          | Ver el tipo de objeto (blob, tree, commit…)  | `git cat-file -t abc1234`                  |
| `git cat-file -p <hash>`          | Mostrar el contenido del objeto              | `git cat-file -p abc1234`                  |
| `git show <hash>`                 | Ver detalles de commits, tags o blobs        | `git show abc1234`                         |
| `git ls-tree <tree_hash>`         | Listar contenido de un objeto tipo tree      | `git ls-tree abc1234`                      |
| `git rev-parse HEAD`              | Obtener el hash del commit actual            | `git rev-parse HEAD`                       |
| `git fsck --full`                 | Verificar y listar todos los objetos         | `git fsck --full`                          |
| `git log --oneline --graph`       | Visualizar commits como nodos del DAG        | `git log --oneline --graph`                |
| `git hash-object -w archivo.txt`  | Crear un blob manualmente y ver su hash      | `git hash-object -w archivo.txt`           |

## **4 ¿Cómo se genera un Git ID?**

- Git calcula el SHA-1 del contenido + tipo + metadatos.
- Un cambio mínimo en el contenido genera un ID completamente distinto.

**Ejemplo**:

```tpl
echo "Hola mundo" | git hash-object --stdin 
```

ó

```tpl
echo "Hola mundo" > fileA.txt
git hash-object fileA.txt  
```

**Resultado**:

{{< highlight powershell >}}
110d7b83bb98c919773ff9b9ee781e71bad210ec
{{< /highlight >}}

## **5 Shortening Git IDs**

- Git permite usar abreviaciones del hash (por ejemplo, los primeros 7–10 caracteres).
- Esto facilita la lectura y referencia en comandos como:

```tpl
git log --oneline
git show abc1234
git rev-parse --short HEAD
```

Puedes configurar la longitud con:

```tpl
git config --global core.abbrev 10
```

## **5 Visualización del flujo de objetos**

{{< mermaid >}}
graph TD
    A[Blob: archivo.txt] --> T[Tree: raíz]
    B[Blob: main.js] --> T
    T --> C[Commit: snapshot]
    C --> Tag[Tag: v1.0]
    classDef obj fill:#e0f7fa,stroke:#00796b,stroke-width:2px,rx:10;
    class A,B,T,C,Tag obj;
{{< /mermaid >}}

## **6 Ejercicio: Crear objetos Git manualmente**

Este ejercicio permite crear un blob, un tree, y finalmente un commit, sin usar **#`git add`#** ni **#`git commit`#**. Todo se hace con comandos internos para revelar cómo Git construye su modelo de objetos.

🎯 **Objetivos del ejercicio**

- Comprender cómo Git representa archivos como blobs.
- Ver cómo los trees organizan blobs en estructuras de directorios.
- Aprender cómo los commits conectan trees y forman el DAG.
- Manipular objetos directamente para entender la arquitectura interna.

🖥️ **Comandos**

{{< highlight powershell >}}
:: ✅ Paso 1 Inicializar un repositorio vacío
git init git-objects-demo
cd git-objects-demo

:: ✅ Paso 2 Crear un archivo y generar su blob
echo "console.log('Hola mundo');" > main.js
git hash-object -w main.js
:: Guarda el contenido como blob y devuelve su SHA-1

:: ✅ Paso 3 Crear un árbol que apunte al blob
git ls-files --stage
:: Verifica el blob en el índice (debe estar vacío aún)

git update-index --add main.js
:: Añade el blob al índice

git write-tree
:: Crea un objeto tree con los blobs del índice
:: Devuelve el SHA-1 del tree

:: ✅ Paso 4 Crear un commit que apunte al tree
GIT_AUTHOR_NAME="Pedro" GIT_AUTHOR_EMAIL="pedro[[@]]example.com" \
git commit-tree <tree_hash> -m "🔨 Commit manual con main.js"
:: Crea un commit que apunta al tree, sin HEAD ni rama

:: ✅ Paso 5 Verificar el commit
git cat-file -p <commit_hash>
:: Muestra el contenido del commit: autor, fecha, mensaje y tree

:: ✅ Paso 6 Crear una rama que apunte al commit
git branch manual-commit <commit_hash>
git checkout manual-commit
:: Ahora HEAD apunta al commit creado manualmente
{{< /highlight >}}

🎨 **Estilo visual**

{{< mermaid >}}
sequenceDiagram
  participant Usuario
  participant Git

  Usuario->>Git: git init git-objects-demo
  Git-->>Usuario: Inicializa repositorio vacío

  Usuario->>Git: echo "Hola mundo" > main.js
  Usuario->>Git: git hash-object -w main.js
  Git-->>Usuario: Crea blob y devuelve SHA-1

  Usuario->>Git: git update-index --add main.js
  Git-->>Usuario: Añade blob al índice

  Usuario->>Git: git write-tree
  Git-->>Usuario: Crea objeto tree y devuelve SHA-1

  Usuario->>Git: git commit-tree tree_hash -m "Commit manual"
  Git-->>Usuario: Crea commit que apunta al tree

  Usuario->>Git: git branch manual-commit commit_hash
  Usuario->>Git: git checkout manual-commit
  Git-->>Usuario: HEAD apunta al commit creado

  Usuario->>Git: git cat-file -p commit_hash
  Git-->>Usuario: Muestra contenido del commit

{{< /mermaid >}}
