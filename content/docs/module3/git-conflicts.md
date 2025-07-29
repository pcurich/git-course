---
title: "Resolving Merge Conflicts"
weight: 31
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1 Revisión de los merges en los conflictos**

### 1.1 ¿Por qué ocurren los conflictos?

Los conflictos de merge se presentan cuando Git no puede aplicar automáticamente los cambios porque dos ramas han editado la misma línea o zona de un archivo, y no sabe cuál conservar. Git detiene el merge e indica que el usuario debe intervenir manualmente.

### 1.2 Comprobación de estado antes del merge

```bash
git status
```

Git indica los archivos con conflictos usando mensajes como:

```bash
Unmerged paths:
  (use "git restore --staged <file>..." to unstage)
        both modified:   archivo.txt
```

Ver los cambios implicados

```bash
git diff
```

O para ver el conflicto directamente en el archivo:

```bash
<<<<<<< HEAD
Contenido desde rama base (ej. main)
=======
Contenido desde rama fusionada (ej. featureX)
>>>>>>> featureX
```

Este marcador indica el área del conflicto.

### 1.3 Opciones de inspección visual

- git log --merge: muestra los commits en conflicto.
- gitk o herramientas GUI (Visual Studio Code, GitKraken) pueden facilitar la comparación visual.

### 1.4 Buenas prácticas para revisión

- Confirmar en qué parte del código se superponen los cambios.
- Evitar resolver el conflicto sin comprender su causa.
- Consultar al autor del otro cambio si es necesario.

## **2 Resolviendo un conflicto de merge**

Cuando Git detecta un conflicto al intentar fusionar dos ramas, intervienen tres commits fundamentales:

- Commit B – El último commit en la rama actual (ej. main). También conocido como "ours" o "mine"
- Commit C – El último commit en la rama que se desea integrar (ej. featureX). Refiere a "theirs"
- Commit A – El antecesor común de ambas ramas. Conocido como "merge base"
Git utiliza estos tres puntos para calcular las diferencias y generar los marcadores de conflicto en los archivos afectados.

{{< mermaid >}}
graph LR
    HEAD[HEAD]:::puntero --> master[master]:::rama
    master --> A[Commit A - Merge Base]:::base
    A --> B[Commit B - Ours]:::actual
    A --> C[Commit C - Theirs]:::externa
    C --> featureX[featureX]:::rama
    %% Etiquetas explicativas
    A -->|merge base| A
    B -->|ours| B
    C -->|theirs| C
    %% Estilos personalizados
    classDef puntero fill:#fff3e0,stroke:#ff6f00,stroke-width:2px,rx:10
    classDef rama fill:#e8f5e9,stroke:#388e3c,stroke-width:2px,rx:10
    classDef base fill:#e0f7fa,stroke:#00796b,stroke-width:2px,rx:10
    classDef actual fill:#e3f2fd,stroke:#1565c0,stroke-width:2px,rx:10
    classDef externa fill:#fce4ec,stroke:#c2185b,stroke-width:2px,rx:10
{{</ mermaid >}}

Pasos para resolver conflictos manualmente:

- Editar el archivo afectado (fileA.txt). Revisa el contenido conflictivo y decide qué fragmentos conservar: los tuyos (ours), los externos (theirs), o una mezcla de ambos.
- Eliminar los marcadores de conflicto. Borra las líneas especiales que Git inserta:

```bash
<<<<<<< HEAD
...tu contenido...
=======
...contenido de featureX...
>>>>>>> featureX
```

- Guardar los cambios y preparar el archivo para el commit. Agrega el archivo al staging area con:

```bash
git add fileA.txt
```

- Completar el merge con un commit. Git reconocerá que el conflicto fue resuelto y permitirá finalizar la fusión:

```bash
git commit -m "Conflicto resuelto entre master y featureX"
```

- (Opcional) Eliminar la rama featureX si ya no se necesita

```bash
git branch -d featureX
```

## **Simulación de conflicto de merge: flujo completo desde GitHub**

### Rama master: flujo y cambio

```bash
# 🌐 Clonar repo remoto y entrar al proyecto
git clone https://github.com/tuusuario/conflicto-demo.git
cd conflicto-demo

# 📝 Crear archivo base en master
echo "Línea original" > archivo.txt
git add archivo.txt
git commit -m "Commit inicial en master"

# ✏️ Realizar cambio divergente en master
echo "Cambio desde master" > archivo.txt
git commit -am "Cambio desde master"

# 🚀 Push hacia remoto
git push origin master
```

### Rama featureX: creación y cambio

```bash
# 🌿 Crear rama featureX desde master
git checkout -b featureX

# ✏️ Realizar cambio distinto en featureX
echo "Cambio desde featureX" > archivo.txt
git commit -am "Cambio desde featureX"

# 🚀 Push hacia remoto
git push origin featureX
```

### Intento de merge y resolución del conflicto (en master)

```bash
# 📍 Volver a master y traer cambios remotos
git checkout master
git pull origin master

# 🔄 Intentar merge con featureX
git merge featureX
# ⛔ Aparece conflicto en archivo.txt

# 🛠️ Resolver manualmente el conflicto
echo "Resolución combinada del conflicto" > archivo.txt
git add archivo.txt
git commit -m "Conflicto resuelto manualmente entre master y featureX"

# 🚀 Push del merge resuelto al remoto
git push origin master

# 🧹 (Opcional) Eliminar rama integrada
git branch -d featureX
git push origin --delete featureX
```

{{< mermaid >}}
graph TD
    RemoteRepo["🌐 Repositorio remoto en GitHub"]:::remoto
    LocalClone["🖥️ Clonación en local"]:::local
    A["🟠 Commit A - Línea original"]:::base
    B["🔵 Commit B - Cambio en master"]:::master
    C["🟣 Commit C - Cambio en featureX"]:::featureX
    Merge["⛔ Merge con conflicto"]:::error
    RemoteRepo --> LocalClone
    LocalClone --> A
    A --> B
    A --> C
    B --> Merge
    C --> Merge
    classDef remoto fill:#e1f5fe,stroke:#0277bd,stroke-width:2px,rx:10
    classDef local fill:#f3e5f5,stroke:#6a1b9a,stroke-width:2px,rx:10
    classDef base fill:#e0f7fa,stroke:#00796b,stroke-width:2px,rx:10
    classDef master fill:#e3f2fd,stroke:#1565c0,stroke-width:2px,rx:10
    classDef featureX fill:#fce4ec,stroke:#c2185b,stroke-width:2px,rx:10
    classDef error fill:#ffebee,stroke:#c62828,stroke-width:2px,rx:10
{{< /mermaid >}}

## **4 Tabla resumen**

| Acción                          | Comando CLI                       |
|---------------------------------|-----------------------------------|
| Ver archivos en conflicto       | `git status`                      |
| Identificar diferencias         | `git diff`                        |
| Resolver manualmente            | Editar + `git add`                |
| Confirmar resolución            | `git commit`                      |
| Cancelar el merge               | `git merge --abort`               |
| Forzar contenido de rama actual | `git checkout --ours <archivo>`  |
| Forzar contenido fusionado      | `git checkout --theirs <archivo>`|
