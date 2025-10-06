---
title: "Git Flow Ciclo"
weight: 44
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## Ciclo de desarrollo con Git Flow

Git Flow es una estrategia de ramificación que organiza el trabajo en proyectos colaborativos, separando claramente el desarrollo de nuevas funcionalidades, correcciones, y versiones estables. A continuación se detallan los principales tipos de ramas y su uso práctico en Visual Studio, complementado con comandos de Git Flow.

### Inicio y finalización de una rama feature

Las ramas `feature` se utilizan para desarrollar nuevas funcionalidades. Se crean desde develop y se integran nuevamente allí una vez completadas.
Comando Git Flow:

```bash
#Agrega nueva funcionalidad
git flow feature start nombre-del-feature
# Desarrolla la funcionalidad
# hacer commit
git flow feature finish nombre-del-feature
#Sale de la rama develop
#Rama a unir = develop
#La rama del feature se elimina
```

- Esto fusiona la rama en develop y la elimina automáticamente.
📦 Preparación de una versión con releaseLas ramas release permiten estabilizar el código antes de publicarlo. Se crean desde develop y se fusionan en main y develop.Comando Git Flow:git flow release start 1.2.0
Pasos en Visual Studio:- Desde develop, crea una rama release/1.2.0.
- Realiza ajustes finales: documentación, número de versión, pruebas.
- Haz commit de los cambios.
- Finaliza la rama:
git flow release finish 1.2.0
- Esto:
- Fusiona en main y crea un tag.
- Fusiona en develop.
- Elimina la rama release.
🧯 Corrección urgente con hotfixLas ramas hotfix se usan para corregir errores críticos en producción. Se crean desde main y se fusionan en main y develop.Comando Git Flow:git flow hotfix start 1.2.1
Pasos en Visual Studio:- Desde main, crea una rama hotfix/1.2.1.
- Aplica la corrección urgente.
- Haz commit y prueba el cambio.
- Finaliza la rama:
git flow hotfix finish 1.2.1
- Esto:
- Fusiona en main y crea un tag.
- Fusiona en develop.
- Elimina la rama hotfix.
🧹 Fusión automática y eliminación de ramasGit Flow automatiza la fusión y limpieza de ramas al finalizar cada ciclo. Visual Studio también permite hacerlo manualmente:En Visual Studio:- Ve a Team Explorer > Branches.
- Clic derecho sobre la rama a fusionar > Merge From.
- Selecciona la rama destino (main o develop).
- Una vez fusionada, clic derecho > Delete para eliminar la rama local.
Comando Git Flow (ejemplo):git flow feature finish login-form
Este comando:- Fusiona feature/login-form en develop.
- Elimina la rama feature.
¿Te gustaría que prepare una tabla comparativa o una guía editable para tus estudiantes con estos flujos? También puedo ayudarte a crear ejercicios prácticos con escenarios reales para cada tipo de rama.