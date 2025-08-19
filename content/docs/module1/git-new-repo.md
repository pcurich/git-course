---
title: "Git New Repo"
weight: 12
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1. ¿Qué hace git init?**

El comando `git init` inicializa un nuevo repositorio Git en el directorio actual. Esto crea una subcarpeta oculta llamada `.git`, que contiene toda la información necesaria para el control de versiones: historial, configuración, objetos, referencias, etc.

```bash
git init
```

Después de ejecutar `git init`, el directorio pasa a ser un repositorio local, y Git comienza a rastrear los archivos que se agreguen

## **2. Relación con las Áreas de Git**

Git organiza el flujo de trabajo en tres áreas principales:

| Working Directory   | Staging Area        | Local Repository       |
|-|-|-|
| Archivos reales en el sistema    | Archivos preparados para commit  | Historial confirmado con commits    |

{{< mermaid >}}
    flowchart LR
        A[Working Directory.
        Archivos editados] -->|git add| B[Staging Area.
        Archivos listos para commit]
        B -->|git commit| C[Local Repository.
        Historial de versiones]
        classDef etapa fill:#e0f7fa,stroke:#00796b,stroke-width:2px,rx:10;
            class A,B,C,D etapa;
{{< /mermaid >}}

{{< hint info >}}
git init crea el entorno donde este flujo puede ocurrir. Sin él, Git no puede rastrear ni versionar archivos.
{{< /hint >}}
