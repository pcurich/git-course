---
title: "Git Locations"
weight: 11

# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1. Working Directory (Directorio de trabajo)**

- Es el lugar donde editas tus archivos directamente.
- Aquí ves los archivos tal como están en tu sistema de archivos.
- Los cambios que haces aún no están registrados por Git

## **2. Staging Area (Área de preparación o índice)**

- Es una zona intermedia donde seleccionas qué cambios quieres incluir en el próximo commit.
- Usas **#`git add`#**  para mover archivos desde el Working Directory al **#`Staging Area`#** .
- Te permite tener control granular sobre qué se guarda en el historial.

## **3. Local Repository (Repositorio local)**

- Es donde Git guarda los **#`commits`#** que haces.
- Se encuentra dentro de la carpeta oculta **#`.git`#**.
- Aquí vive el historial completo de tu proyecto.
- Usas **#`git commit`#**  para mover los cambios del Staging Area al repositorio local.

## **4. Remote Repository (Repositorio remoto)**

- Es una copia del repositorio alojada en un servidor como GitHub, GitLab o Bitbucket.
- Permite colaboración entre desarrolladores.
- Usas **#`git push`#** para enviar tus **#`commits`#** al remoto, y  **#`git pull`#**  para traer cambios.

Flujo típico de trabajo
Working Directory → git add → Staging Area → git commit → Local Repository → git push → Remote Repository

## **¿Por qué es importante entender esto?**

{{< hint info >}}

Te ayuda a evitar errores como hacer commits incompletos, ademas facilita la colaboración y resolución de conflictos y te permite usar Git como una máquina del tiempo para tu código.
{{< /hint >}}

## **Resumen**

{{< mermaid >}}
    flowchart LR
        A[Working Directory] -->|git add| B[Staging Area]
        B -->|git commit| C[Local Repository]
        C -->|git push| D[Remote Repository]
        D -->|git pull| C

        classDef etapa fill:#e0f7fa,stroke:#00796b,stroke-width:2px,rx:10;
        class A,B,C,D etapa;

{{< /mermaid >}}
