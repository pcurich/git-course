---
title: "Git Networking"
weight: 33
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## **1 Git Networking: clone, fetch, pull, push**

Estos comandos controlan la comunicación entre tu repositorio local y el remoto (origin). Cada uno tiene un rol específico en la sincronización de datos:

| Comando | ¿Qué hace? | ¿Modifica el árbol de trabajo local? |
|---------|---------|---------|
| git clone | Crea una copia del repositorio remoto en local | ✅ Sí |
| git fetch | Trae los cambios del remoto pero no los aplica | ❌ No |
| git pull | Equivale a fetch + merge o rebase | ✅ Sí |
| git push | Envía tus commits locales al repositorio remoto | ❌ No (actúa sobre el remoto) |

## 1.1 Escenario base: `git clone`

```bash
# Clonar un repositorio remoto
git clone https://github.com/usuario/mi-repo.git
cd mi-repo

# Ver ramas remotas
git branch -r
```

{{< mermaid >}}
gitGraph
   commit id: "Commit remoto A" tag: "origin/main"
   checkout main
   commit id: "Commit local A (copiado por clone)" tag: "HEAD ➜ main"
{{</ mermaid >}}

## 1.2 Sincronización: `fetch`

### ¿Qué es `git fetch`?

`git fetch` es un comando que sincroniza tu repositorio local con el remoto sin alterar tu historial local. Es una forma segura de actualizar tus referencias a lo que ha ocurrido en el repositorio remoto (como nuevos `commits` o `ramas`), sin aplicar esos cambios directamente en tu trabajo actual.

### ¿Qué hace exactamente?

- Contacta el remoto (`origin`, por ejemplo) y descarga nuevos `commits`, `ramas` o `etiquetas`.
- Actualiza las referencias remotas como `origin/main`, `origin/feature-x`, etc.
- No modifica tu rama actual (`main`, por ejemplo), ni realiza fusiones automáticamente.
- Ideal para revisar antes de tomar decisiones: puedes inspeccionar los cambios antes de `merge` o `rebase`.

### Ejemplo: usando `git fetch`

```bash
# Traer referencias remotas sin modificar el árbol local
git fetch origin

# Ver diferencias
git log HEAD..origin/main --oneline
```

## 1.3 Sincronización: `git pull`

### ¿Qué es `git pull`?

`git pull` es un comando compuesto que sincroniza una rama local con su contraparte remota. Internamente, Git lo descompone así:

- `git fetch`: descarga los últimos cambios del remoto, sin aplicarlos.
- `git merge FETCH_HEAD`: aplica (fusiona) esos cambios sobre la rama local activa

{{< hint info >}}
`FETCH_HEAD` es un puntero temporal que Git usa para referenciar los datos recién traídos por fetch.
{{</ hint  >}}

### Ejemplo: usando `pull`

```bash
# Obtener y fusionar cambios
git pull origin main

# Ver historial
git log --oneline --graph --decorate
```

### Ejemplo CLI: flujo interno de `git pull`

```bash
# Paso explícito (fetch manual)
git fetch origin main

# `FETCH_HEAD` ahora apunta al último commit remoto
cat .git/FETCH_HEAD

# Fusionamos manualmente los cambios traídos
git merge FETCH_HEAD

# Esto es exactamente lo que hace
git pull origin main
```

## 1.4 Opciones de fusión local en `git pull` y `git merge`

Las opciones de merging strategy determinan cómo Git fusiona los commits locales y remotos cuando hay diferencias.

| Opción | Comportamiento | ¿Cuándo usarlo? |
|-|-|-|
| `--ff` | Fast-forward si es posible (default); no crea commit de merge | Historial lineal, sin conflictos |
| `--no-ff` | Siempre crea commit de merge, incluso si puede hacer fast-forward | Para preservar historia de integración |
| `--ff-only` | Solo acepta fast-forward; falla si requiere merge | Evita commits de merge |
| `--rebase` | Reescribe historial local encima de los commits remotos | Para mantener un historial limpio |
| `--rebase=merges` / `--rebase --preserve-merges` | Reescribe con conservación de merges | Útil en equipos que usan integración estructurada |

### Ejemplo CLI: uso de opciones

```bash
# Pull con fast-forward automático (default)
git pull --ff origin main

# Pull con commit de merge forzado
git pull --no-ff origin main

# Pull solo si se puede fast-forward (falla si hay divergencia)
git pull --ff-only origin main

# Pull con rebase (evita commits de merge)
git pull --rebase origin main
```

### 1.4.1 Git pull usando fast-forward

**¿Cuándo ocurre un fast-forward?**

Git puede aplicar un fast-forward si:

- Estás en una rama local (ej. main) sin commits nuevos.
- La rama remota (origin/main) tiene commits más avanzados.
- Ambas ramas comparten un historial lineal sin divergencia.
El pull se resuelve avanzando el puntero local al nuevo commit remoto, sin crear un commit de merge.

**Escenario típico:**

Tu rama local está desactualizada pero intacta. Al ejecutar:

```bash
# Ver estado actual
git status

# Actualizar con fast-forward
git pull --ff origin main

# Confirmar resultado
git log --oneline --graph
```

```tpl
Antes del pull:

origin/main:  A---B---C
local:        A

Después del pull (fast-forward aplicado):

origin/main:  A---B---C
local:        A---B---C   ← HEAD actualizado
```

{{< hint info >}}
La rama local simplemente avanzó. No hay commit extra ni línea de fusión. Esto es lo que representa --ff: un avance limpio del puntero.
{{< /hint >}}

### 1.4.2 Git pull con conflictos de cambios no comiteados

**¿Cuándo ocurre este escenario?**
Sucede cuando:

- Tu rama local está detrás de la remota (estado behind).
- Git podría aplicar un fast-forward...
- ...pero tienes cambios modificados localmente que entran en conflicto con los commits remotos.
- No se puede aplicar el pull hasta que resuelvas los cambios locales (conflicto entre el árbol de trabajo y el historial remoto).

**Escenario típico:**

```bash
# Simulamos una edición local en conflicto
echo "Cambios locales" >> index.html

# Estado antes del pull
git status

# Intentar hacer un pull fast-forward
git pull --ff origin main

### Merge commit (--no-ff)
```

🔴 Resultado:

```bash
error: Your local changes to the following files would be overwritten by merge:
    index.html
Please commit your changes or stash them before you merge.
Aborting
```

```tpl
Remoto:        A---B---C   (origin/main)
Local:         A---(cambios sin confirmar)
                    \
                     \   ← HEAD en A con index.html modificado

git pull --ff        ✘ error: no se puede avanzar sin perder cambios locales
```

**¿Cómo resolverlo?**

```bash
# Opción 1: confirmar cambios locales
git add index.html
git commit -m "Cambios locales"

git pull --ff origin main   # Ahora puede hacer merge si es posible

# Opción 2: guardar los cambios temporalmente
git stash

git pull --ff origin main   # Se aplicará el fast-forward
git stash pop               # Recuperas tus ediciones
```

### 1.4.3 Git pull con merge (`--no-ff`)

**¿Cuándo se usa `--no-ff`?**

- En ramas colaborativas (como feature-x, hotfix, etc.) donde quieres mantener un punto de unión explícito.
- Evita que Git simplemente avance la rama (fast-forward) como si los cambios vinieran del mismo flujo.
- Se crea un commit de merge, incluso si el historial permitiría avanzar directamente

**Escenario típico:**

```bash
# Nos aseguramos de tener una divergencia local/remota
git fetch origin

# Intentamos hacer un pull con merge explícito
git pull --no-ff origin main
```

🔵 Resultado

```bash
Merge made by the 'recursive' strategy.
 files changed: 2
 create mode: foo.js
```

Esto crea un commit de tipo "merge", agrupando las diferencias locales y remotas.

```tpl
Remoto:         A---B---C   (origin/main)
                \
Local:           D---E      (main local)
                 \
                  \__ ⬅️ Merge commit con --no-ff
                         F

Resultado:       A---B---C---F   (main)
                         /   \
                       D-----E
```

{{< hint info >}}
Aquí, F es un commit de merge explícito, que preserva el origen de los cambios. No se aplastan (squash) ni se avanza en línea recta.
{{< /hint >}}

**Ventajas de `--no-ff`**

- Historial más legible para auditorías y revisión de código.
- Útil en flujos tipo feature branches donde se quiere conservar el contexto completo de la fusión.
- Permite revertir cambios en bloque (por commit de merge)

## 1.5 Sincronización: push

El comando `git push` envía los `commits` locales al repositorio `remoto`, actualizando la rama correspondiente allí (como `origin/main`). Es fundamental para compartir tu trabajo con otros colaboradores o simplemente sincronizar tu copia local con el remoto

**Escenario típico:**

```bash
# Agregamos un cambio local
echo "Nueva sección" >> archivo.md
git add archivo.md
git commit -m "Agrega nueva sección"

# Enviamos al remoto
git push origin main
```

### 1.5.1 ¿Qué hace la opción `-u` ?

La opción -u (forma corta de --set-upstream) define una relación de seguimiento entre tu rama local y la remota. Esta relación hace que futuros git push y git pull se puedan ejecutar sin especificar la rama:

```bash
git push -u origin main
```

Este comando:

- Envía la rama main al remoto origin.
- Configura main como tracking branch de origin/main.
- Permite usar simplemente git pull o git push en adelante, sin indicar origin main

### 1.5.2 Diagnóstico de seguimiento con `-vv`

```bash
git branch -vv

* main  abc1234 [origin/main] Agrega nueva sección
```

{{< hint info >}}
Aquí el [origin/main] indica que main sigue a la rama remota, gracias al -u
{{< /hint >}}

```tpl
Antes del push:

Local:   A---B   (main)
Remoto:  A       (origin/main)

Después del push -u:

Local:   A---B   (main <-> origin/main)
Remoto:  A---B   (origin/main)
```

{{< hint info >}}
El símbolo <-> representa la relación de seguimiento establecida por `-u`
{{< /hint >}}

### 1.5.3 Buenas prácticas

- Usa `git push -u origin <branch>` al crear una rama nueva, para facilitar futuras operaciones.
- Si ya hiciste push, puedes agregar el upstream con:

```bash
git branch --set-upstream-to=origin/main
```

### 1.5.4 Errores frecuentes en `git push`

| 🔍 Mensaje CLI | 💬 Explicación clara | 💡 Solución didáctica |
|-|-|-|
| rejected | El remoto tiene commits que tú no tienes. | Usa `git pull` primero para sincronizar. |
| non-fast-forward | Tu push sobrescribe historia existente. Git lo bloquea por seguridad. | Usa `git pull --rebase` o `git push --force-with-lease` con cautela. |
| no upstream configured | La rama local no tiene una rama remota asociada. | Añade upstream: `git push -u origin <branch>` |
| permission denied | No tienes acceso al remoto (credenciales, permisos, SSH). | Verifica token de acceso, SSH keys o tus privilegios. |
| fatal: repository not found | URL incorrecta o el repo no existe (a veces error de autenticación). | Verifica la URL del remoto: `git remote -v` |

### 1.5.5 Alternativas de push para casos conflictivos

**`git push --force`**
✅ Reescribe la rama remota sin pedir permiso.
⛔ Puede borrar commits ajenos: peligroso para repos colaborativos.

**`git push --force-with-lease`**
✅ Fuerza el push solo si nadie más ha actualizado el remoto desde tu último pull.
👍 Más seguro: evita sobrescribir trabajo reciente de otros

```tpl
Situación: conflicto de ramas

Local:    A---B---C   (main)
Remoto:   A---B---X   (origin/main)

Intento de push:
        ❌ rejected (non-fast-forward)

Resolución:
        ✅ pull --rebase
        ✅ push --force-with-lease
```

{{< hint info >}}
El commit X en remoto no está en local. Por eso el push se rechaza hasta resolver el conflicto
{{< /hint >}}
