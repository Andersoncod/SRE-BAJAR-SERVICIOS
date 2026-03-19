Perfecto — aquí tienes el README **numerado y con tabla de contenido**, como se vería profesionalmente en Markdown:

---

# Pipeline de Control de Flujo de Remesas

## Tabla de Contenido

1. [Objetivo](#1-objetivo)
2. [Beneficios](#2-beneficios)
3. [Parámetros del Pipeline](#3-parámetros-del-pipeline)
   3.1 [Ambiente](#31-ambiente)
   3.2 [Item](#32-item)
   3.3 [Opción](#33-opción)
   3.4 [Reiniciar Micro](#34-reiniciar-micro)
4. [Flujo de Ejecución](#4-flujo-de-ejecución)
5. [Proceso Técnico](#5-proceso-técnico)
   5.1 [Aprobación](#51-aprobación)
   5.2 [Asunción de Rol](#52-asunción-de-rol)
   5.3 [Modificación en Base de Datos](#53-modificación-en-base-de-datos)
   5.4 [Reinicio de Microservicio](#54-reinicio-de-microservicio)
6. [Resultado](#6-resultado)
7. [Tecnologías Utilizadas](#7-tecnologías-utilizadas)
8. [Casos de Uso](#8-casos-de-uso)
9. [Buenas Prácticas](#9-buenas-prácticas)
10. [Riesgos Mitigados](#10-riesgos-mitigados)
11. [Autor](#11-autor)

---

## 1. Objetivo

Este pipeline permite habilitar o inhabilitar de forma controlada y granular el flujo de remesas por aliado (remesadora o ProductId específico), evitando afectar transversalmente a todos los integradores cuando se presenta una falla puntual.

Actualmente, la desactivación total del servicio requiere bajar componentes completos, lo que genera:

* Encolamiento masivo de remesas
* Impacto general a todos los aliados
* Interrupciones operativas en producción

Con este pipeline se modifica directamente el atributo `isActive` en las tablas de configuración, permitiendo aislar el aliado afectado sin interrumpir el procesamiento global.

---

## 2. Beneficios

* Desactivación inmediata por aliado
* Aislamiento de incidentes
* Continuidad operativa
* Reducción de impacto masivo
* Ejecución auditable y controlada
* Automatización segura para soporte

---

## 3. Parámetros del Pipeline

### 3.1 Ambiente

Define dónde se ejecutarán los cambios.

**Valores:**

* `qa` — Ambiente de pruebas
* `pdn` — Ambiente productivo

---

### 3.2 Item

Identificador del aliado a modificar.

Puede recibir múltiples valores separados por coma.

**Ejemplo:**

```
PROD123, PROD456, PROD789
```

---

### 3.3 Opción

Acción a ejecutar sobre el flujo.

**Valores:**

* **Habilitar** → Permite el procesamiento de remesas
* **Inhabilitar** → Bloquea el procesamiento del aliado

---

### 3.4 Reiniciar Micro

Indica si se debe forzar redeploy del microservicio tras el cambio.

**Valores:**

* **Si** → Fuerza reinicio
* **No** → No realiza reinicio

---

## 4. Flujo de Ejecución

1. Usuario ejecuta pipeline manualmente
2. Selecciona ambiente
3. Ingresa ProductId(s)
4. Selecciona acción
5. Define si reinicia microservicio
6. Solicita aprobación
7. Pipeline ejecuta automatización

---

## 5. Proceso Técnico

### 5.1 Aprobación

Se requiere aprobación manual del equipo autorizado antes de ejecutar cambios en infraestructura.

---

### 5.2 Asunción de Rol

El pipeline:

* Obtiene credenciales temporales
* Asume rol de operación
* Garantiza seguridad mediante sesiones limitadas

---

### 5.3 Modificación en Base de Datos

Se actualiza el flag:

```
isActive = true / false
```

para cada ProductId indicado.

---

### 5.4 Reinicio de Microservicio

Si se solicita:

* Se fuerza nuevo deployment
* Se refresca configuración en caliente

---

## 6. Resultado

Tras ejecución exitosa:

* Aliado habilitado o inhabilitado inmediatamente
* No se afectan otros aliados
* No se detiene el flujo global
* No se generan encolamientos masivos
* Sistema mantiene estabilidad operativa

---

## 7. Tecnologías Utilizadas

* Azure DevOps Pipelines
* Amazon Web Services (AWS)
* DynamoDB
* ECS (Elastic Container Service)
* Python (boto3)
* Bash scripting
* IAM Roles

---

## 8. Casos de Uso

* Fallas de integración con un aliado
* Mantenimiento controlado por remesadora
* Aislamiento de incidentes productivos
* Mitigación rápida sin despliegues
* Soporte operativo de alta prioridad

---

## 9. Buenas Prácticas

* Ejecutar solo con aprobación
* Validar correctamente los ProductId
* Usar reinicio solo si es necesario
* Registrar ticket asociado al cambio
* Ejecutar primero en QA cuando aplique

---

## 10. Riesgos Mitigados

* Caídas masivas del servicio
* Impacto transversal en producción
* Interrupciones innecesarias
* Despliegues de emergencia
* Escalamientos operativos críticos

---

## 11. Autor

Equipo de ASD moneda extranjera
