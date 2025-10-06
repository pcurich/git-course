---
title: "Git Flow Branch"
weight: 43
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

## Estructura de ramas en Git Flow

Git Flow propone una estrategia de ramificación que organiza el desarrollo en ramas con roles específicos. Esta estructura facilita la colaboración, el control de versiones y la entrega continua.

## Semantica de ramas

|Rama|Descripción|Rama padre|Con quien se hace merge|Se puede etiquetar|prefijo de la etiqueta|
|-|-|-|-|-|-|
|`main`|La rama principal del repositorio. Esta rama refleja el estado de producción del software|-|-|NO|-|
|develop|Rama de integración donde se fusionan todas las caractertisticas completadas. Refleja el estado actual del desarrollo en curso|`main`|`main`|NO|-|
|feature/|Ramas para desarrollar nuevas caracteristicas / funcionalidades|`develop`|`develop`|NO|-|
|release/|Ramas para preparar una nueva versión del software para el lanzamiento|`develop`|`main` `develop`|SI|v|
|hotfix/|Ramas para corregir errores criticos en producción|`main`|`main` `develop` `release`|NO|-|
|support/|Ramas para corregir errores no criticos |`develop`|`develop`|NO|-|
