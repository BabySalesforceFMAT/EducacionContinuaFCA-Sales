# Especificación de Caso de Uso: Consultar Tareas y Recordatorios

**ID:** CU-VE-009  
**Actor(es):** Promotor de Ventas (Agente), Responsable de Ventas (Gerente)  
**Objeto Salesforce:** Task (Tarea)

---

## 1. Descripción
El usuario accede a una vista centralizada para revisar su agenda de trabajo pendiente. Esto incluye llamadas programadas, correos por enviar y seguimientos automáticos generados por el sistema. El objetivo es que el Promotor visualice claramente qué debe hacer hoy, qué tiene atrasado y qué viene en el futuro.

## 2. Precondiciones
1.  **Autenticación:** El usuario ha iniciado sesión.
2.  **Datos:** Existen tareas asignadas al usuario (o a su equipo, en el caso del Gerente).

## 3. Flujo Básico: Vista del Agente (Mi Agenda)

1.  **Navegación:** El Promotor tiene dos opciones principales para iniciar:
    * **Opción A (Página de Inicio):** Desde la *Home Page*, visualiza el componente **"Tareas de hoy"** (*Today's Tasks*).
    * **Opción B (Pestaña Tareas):** Hace clic en la pestaña **Tareas** en la barra de navegación para una vista completa.
2.  **Visualización Predeterminada:** El sistema muestra la Lista de Tareas filtrada por defecto (generalmente "Abiertas recientemente" o "Tareas de hoy").
3.  **Filtrado Temporal (List Views):** El Promotor cambia la vista de lista usando el selector desplegable para ver diferentes periodos:
    * Selecciona **"Tareas de hoy"**: Para ver lo urgente del día.
    * Selecciona **"Vencidas"** (*Overdue*): Para ver lo que no se completó en días anteriores. *Nota: Salesforce muestra las fechas vencidas en color rojo automáticamente.*
    * Selecciona **"Próximos 7 días"**: Para planificar la semana.
4.  **Revisión de Detalles:** En la lista, el Agente ve columnas clave: *Asunto*, *Fecha de Vencimiento*, *Prioridad* y *Relacionado con* (Nombre de la Oportunidad/Lead).
5.  **Acción Directa:**
    * Al hacer clic en el **Asunto** de la tarea, entra al detalle de la misma.
    * Al hacer clic en el enlace de **"Relacionado con"**, es redirigido directamente a la ficha de la Oportunidad para comenzar a trabajar.

## 4. Flujos Alternativos

### A. Marcado Rápido (Completar desde la Lista)
* **Contexto:** El usuario ya realizó la acción (ej. ya envió el correo) y solo quiere "tachar" el pendiente.
* **Acción:** En la vista de lista de Tareas, marca la casilla de verificación situada a la izquierda del registro.
* **Resultado:** La tarea cambia de estado a "Completed", desaparece de la lista de "Tareas Abiertas" y se mueve al historial de la Oportunidad.

### C. Visualización de "Delegadas"
* **Contexto:** El Promotor asignó una tarea a otro compañero y quiere ver si ya se hizo.
* **Acción:** Selecciona la vista de lista **"Delegadas"** (*Delegated Tasks*).
* **Resultado:** Ve las tareas que él creó pero que son propiedad de otro usuario actualmente.

## 5. Diccionario de Datos en Listado

Para que la consulta sea eficiente, las Vistas de Lista deben configurarse con estas columnas:

| Columna | API Name | Función Visual |
| :--- | :--- | :--- |
| Asunto | `Subject` | Describe qué hay que hacer. |
| Fecha de Vencimiento | `ActivityDate` | **Crítico.** Si es < Hoy, se muestra en **Rojo**. |
| Estado | `Status` | Indica progreso (No iniciada, En curso). |
| Prioridad | `Priority` | Alta (suele mostrar un icono de alerta), Normal. |
| Relacionado con | `WhatId` | Enlace a la Oportunidad/Cuenta. |
| Nombre | `WhoId` | Enlace al Contacto/Lead. |

## 6. Post-condiciones

1.  **Navegación:** El usuario es redirigido al contexto de trabajo (la Oportunidad) para ejecutar la tarea.
2.  **Productividad:** El usuario tiene claridad sobre sus prioridades inmediatas sin tener que buscar registro por registro.

---

<img width="11276" height="4048" alt="CU-VE-009" src="https://github.com/user-attachments/assets/5f583cba-863b-4871-8dc8-e47105268ab1" />
