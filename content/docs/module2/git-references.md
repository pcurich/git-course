---
title: "Git References"
weight: 24
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1. Branch Labels & HEAD**

### **1.1. Conceptos Clave**

- Branch labels son punteros móviles a commits.
- HEAD es el puntero simbólico al commit actual (usualmente el último de la rama activa).

{{< mermaid >}}
graph LR
  A[Commit A] --> B[Commit B] --> C[Commit C]
  C -->|master| HEAD
{{< /mermaid >}}

#### ¿Qué es un commit en Git?

Un commit representa un punto específico en el historial del repositorio. Es una instantánea del estado de los archivos (y sus metadatos) en un momento dado. Cada commit tiene:

- Un hash único que lo identifica.
- Uno o más padres, salvo el inicial.

```bash
git commit -m "Tu mensaje aquí" 
# Esto crea un nuevo commit, que hereda como “padre” el commit anterior.
```

#### ¿Qué es un padre?

Un padre es simplemente el commit que precede a otro en el historial. Es la conexión que mantiene la estructura en forma de grafo dirigido (DAG). Git usa estas relaciones para reconstruir la línea de tiempo y las fusiones.

- Cuando usas HEAD^, estás accediendo al padre directo del commit apuntado por HEAD.

#### Diferencia entre “commit” y “padre”

| Concepto | Descripción |
|--------------|---------------|
| Commit | Nodo en el historial; contiene cambios, mensaje, autor y referencia única. |
| Padre | Relación entre commits; indica de dónde proviene un commit específico. |

Todos los padres son commits, pero no todos los commits son padres (ej. un commit puede no tener hijos si es el último).

#### Ejemplo visual

```bash
C0 --- C1 --- C2 (HEAD)
```

- C2 es un commit (el actual).
- C1 es su padre directo, accesible con HEAD^.
- C0 es abuelo, accesible con HEAD^^ o HEAD~2.
Y si C2 fuera un merge, podría tener dos padres:

```bash
           C2 (merge)
          /    \
     C1-B1     C1-B2
```

- HEAD^ → primer padre (normalmente, rama activa).
- HEAD^2 → segundo padre (la rama fusionada).

### **1.2. Estado de HEAD y ramas**

| Estado       | HEAD apunta a | Efecto en commits nuevos |
|--------------|---------------|---------------------------|
| Rama activa  | Último commit de la rama | Se agregan a la rama |
| Detached HEAD| Commit específico         | No se agregan a ninguna rama |

### **1.3. Ejercicio práctico**

```bash
git checkout master
git log --oneline --graph --decorate
```

Haz checkout a un commit específico y observa el estado de HEAD:

```bash
git checkout commit_id 
```

## **2. Referencias a commits anteriores (~ y ^)**

### **2.1 Uso semántico**

- ^: padre directo del commit.
- ~n: n commits hacia atrás en línea directa.

{{< mermaid >}}
graph TD
  A["Commit A (HEAD~3)"]
  B["Commit B (HEAD~2)"]
  C["Commit C (HEAD^ o HEAD~1)"]
  D["Commit D (HEAD)"]

  A --> B --> C --> D

{{< /mermaid >}}

### **2.2 Accesos relativos**

| Referencia   | Qué muestra                                | Equivalente a      |
|--------------|---------------------------------------------|--------------------|
| HEAD^        | Padre directo del commit actual             | HEAD~1             |
| HEAD^^       | Abuelo del commit actual                    | HEAD~2             |
| HEAD^^^      | Bisabuelo (tercer padre en la cadena)       | HEAD~3             |
| HEAD~1       | Un commit atrás en línea directa            | HEAD^              |
| HEAD~2       | Dos commits atrás en línea directa          | HEAD^^             |
| HEAD~3       | Tres commits atrás en línea directa         | HEAD^^^            |
| HEAD^2       | Segundo padre en un commit con múltiples padres (merge) | -      |
| HEAD~4       | Cuatro commits atrás                        | HEAD^^^^           |
| master^      | Padre del último commit en la rama master   | master~1           |
| HEAD~0       | El commit actual (sin retroceder)           | HEAD               |

### **2.3 Ejemplo 1**

```bash
git log --oneline
git show HEAD^
git show HEAD~2
```

### **2.4 Ejemplo 2**

{{< mermaid >}}
gitGraph
   commit id: "C0 (main)" tag: "Inicio"
   branch B1
   checkout B1
   commit id: "C1-B1"
   commit id: "C2-B1"
   checkout main
   branch B2
   checkout B2
   commit id: "C1-B2"
   commit id: "C2-B2"
   checkout main
   branch B3
   checkout B3
   commit id: "C1-B3"
   commit id: "C2-B3"
   checkout B1
   merge B2 tag: "C3 (Merge B1+B2)"
   checkout B3
   merge B1 tag: "C4 (Merge C3+B3)"
{{< /mermaid >}}

| Commit             | Qué representa                            | Comando desde HEAD    |
|--------------------|--------------------------------------------|------------------------|
| C4 (Final Merge)   | Último commit (merge de B3 + C3)           | `HEAD`                 |
| C3 (Merge B1+B2)   | Primer padre del merge final               | `HEAD^`                |
| C2-B3              | Segundo padre del merge final              | `HEAD^2`               |
| C2-B1              | Padre izquierdo del merge de B1 y B2       | `HEAD^^`               |
| C2-B2              | Segundo padre del merge entre B1 y B2      | `HEAD^2^`              |
| C1-B1              | Commit anterior en la rama B1              | `HEAD^^~1`             |
| C1-B2              | Commit anterior en la rama B2              | `HEAD^2^~1`            |
| C1-B3              | Commit anterior en la rama B3              | `HEAD^2~1`             |
| C0 (main)          | Ancestro común                             | `HEAD^^~2`             |

```bash
git show HEAD 
git log --graph --oneline --all
```

## **3. Tags en Git**

### **3.1. Concepto**

- Los tags son referencias fijas (no móviles) a commits.
- Muy útiles para marcar versiones o hitos importantes.

{{< mermaid >}}
graph LR
  A[Commit A] --> B[Commit B] --> C[Commit C]
  C -->|v1.0| Tag
{{< /mermaid >}}

### **3.2. Tipos de tags**

| Tipo       | Descripción                  | Ejemplo          |
|------------|------------------------------|------------------|
| Lightweight| Puntero directo al commit    | git tag v1.0     |
| Annotated  | Incluye metadatos y mensaje  | git tag -a v1.0 -m "Release v1.0" |

### **3.3 Comandos útiles**

```bash
git tag #Muestra todos los tags en el repositorio
git tag v1.0 commit_id #Especifica a que commit se coloca el tag (default HEAD)
git tag -a v1.0 -m "Versión estable"
git show v1.0
git push <remote> <tagneme> #Envia un solo tag
git push <remote> --tags #Envia todos los tags
```

## **4. HEAD y las Referencias Simbólicas**

Una referencia simbólica (symbolic reference) es un alias que apunta a otra referencia en Git. El ejemplo más común es HEAD, que normalmente apunta simbólicamente al nombre de una rama (como refs/heads/master), en lugar de a un commit específico.

- Si HEAD está vinculado simbólicamente a una rama, los commits nuevos se agregan a esa rama.
- Si HEAD se encuentra detached, entonces apunta directamente a un commit y no actualiza ninguna rama.

### **4.1 Comportamientos comunes**

| Estado de HEAD     | Tipo de referencia     | Apunta a                | Comportamiento          |
|--------------------|------------------------|-------------------------|--------------------------|
| HEAD normal        | Simbólica              | `refs/heads/main`       | Nuevos commits en rama  |
| HEAD detached      | Directa                | ID de commit (`abc123`) | No afecta ramas         |

### **4.2 Ejemplo**

```bash
# Ver la ruta simbólica de HEAD
cat .git/HEAD

# Resultado posible:
# ref: refs/heads/main
```

```bash
# Cambiar a un commit específico (estado detached HEAD)
git checkout commit_id
cat .git/HEAD
# Resultado:
# commit_id (ya no es una referencia simbólica)
```

### **4.3 Diagrama explicativo**

{{< mermaid >}}
graph TD
  subgraph Git Internals
    A[refs/heads/main → Commit D]
    B[HEAD → refs/heads/main]
  end

  style B fill:#e0f7fa,stroke:#00796b,stroke-width:2px,rx:10;
{{< /mermaid >}}

Este diagrama muestra que HEAD apunta simbólicamente a refs/heads/main, que a su vez apunta al commit actual. Si haces checkout a un commit específico, HEAD se desconecta de main y deja de ser simbólica.
