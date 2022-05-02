# Ejercicios

## Pre-requisitos para ejecutar las aplicaciones en local

* Docker
* node 12.* >

### Además para construir la aplicación en local para cada ejercicio podemos valernos de lo publicado en: https://github.com/Lemoncode/bootcamp-devops-lemoncode/tree/master/02-orquestacion/exercises Donde además se nos explica cómo proceder para cada ejercicio de forma concreta:

* Ejercicio 1: https://github.com/Lemoncode/bootcamp-devops-lemoncode/blob/master/02-orquestacion/exercises/00-monolith-in-mem/exercise-monolith-in-memory.md

* Ejercicio 2: https://github.com/Lemoncode/bootcamp-devops-lemoncode/blob/master/02-orquestacion/exercises/01-monolith/exercise-monolith.md

* Ejercicio 3: https://github.com/Lemoncode/bootcamp-devops-lemoncode/blob/master/02-orquestacion/exercises/02-distributed/exercise-ingress.md

### Para las referencias sobre Kubernetes-Ingress he seguido esta parte de la documentación oficial:
· https://kubernetes.io/docs/concepts/services-networking/ingress/

### Además del repo propio para nginx:
· https://github.com/kubernetes/ingress-nginx/blob/main/README.md#readme


## Ejercicio 1. Monolito en memoria.

Se trata de una aplicación web que expone una aplicación de UI (la típica aplicación TODO), y una API para gestionar en servidor los 'TODOS'. La persistencia de los TODOS es en memoria, eso significa que cuando apaguemos la aplicación dejarán de persistir los datos.

_Solución: directorio `DevOps-Bootcamp-Exercises\Kubernetes\1MonolitoEnMemoria`_

## Ejercicio 2. Monolito.

Se trata de la misma aplicación pero en este caso la persistencia en memoria se hace a través de una base de datos.

_Solución: directorio `DevOps-Bootcamp-Exercises\Kubernetes\2MonolitoPersistente`_

## Ejercicio 3. Aplicación Distribuida

See trata de dos aplicaciones, una UI expuesta a tarvés de un `nginx` y una `API` que corre sobre `express/nodejs` y se conecta con una base de datos `postgres`.

_Solución: directorio `DevOps-Bootcamp-Exercises\Kubernetes\3AppDistribuida`_