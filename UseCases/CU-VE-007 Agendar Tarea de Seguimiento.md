# Especificación de Caso de Uso: Agendar Tarea de Seguimiento

**ID:** CU-VE-007  
**Actor(es):** Promotor de Ventas (Agente)  
**Objeto Salesforce:** Task (Tarea)

---

## 1. Descripción
El Promotor de Ventas programa una actividad futura (llamada, correo, reunión) asociada a una Oportunidad o Lead. El objetivo es garantizar que el sistema le recuerde realizar una acción específica en una fecha determinada, evitando que el prospecto se "enfríe" o se pierda por falta de seguimiento.

## 2. Precondiciones
1.  **Autenticación:** El Promotor ha iniciado sesión en Salesforce.
2.  **Contexto:** Existe un registro activo (Lead u Oportunidad) sobre el cual se requiere realizar una acción futura.
3.  **Permisos:** El usuario tiene permisos de escritura en el objeto `Task`.

## 3. Flujo Básico (Camino Feliz)

1.  **Navegación:** El Promotor accede a la ficha del Lead o de la Oportunidad que está gestionando.
2.  **Panel de Actividad:** Localiza el componente de "Actividad" (Activity Composer), generalmente situado a la derecha o en el centro de la pantalla.
3.  **Selección:** Hace clic en la pestaña **"Nueva Tarea"** (*New Task*).
4.  **Captura de Datos:** El sistema despliega el formulario rápido. El Promotor ingresa:
    * **Asunto (Subject):** Selecciona de la lista (ej. "Llamada", "Enviar Cotización") o escribe un asunto personalizado.
    * **Fecha de Vencimiento (Due Date):** Selecciona la fecha en el calendario (ej. "Próximo lunes").
    * **Asignado a (Assigned To):** Por defecto aparece su propio usuario (Auto-asignación).
    * **Relacionado con (Related To):** El sistema precarga automáticamente la Oportunidad/Lead actual.
5.  **Guardar:** El Promotor hace clic en el botón **"Guardar"**.
6.  **Confirmación del Sistema:**
    * Aparece un mensaje flotante (Toast) indicando: *"Tarea [Asunto] creada"*.
    * La tarea aparece inmediatamente en la sección **"Próximos pasos"** (*Next Steps*) dentro de la Cronología de Actividades.
    * El registro de la Oportunidad/Lead se actualiza internamente (se refresca la fecha de "Próxima actividad").

## 4. Flujos Alternativos y Excepciones

### A. Modificar una Tarea Existente
* **Contexto:** El cliente cambió la fecha de la reunión o el Promotor necesita posponer la llamada.
* **Acción:**
    1.  En la Cronología de Actividades, busca la tarea en la sección "Próximos pasos".
    2.  Hace clic en el menú desplegable (flecha hacia abajo) de la tarea y selecciona **"Editar"** o **"Cambiar fecha"**.
    3.  Actualiza la fecha de vencimiento y guarda.

### B. Delegación de Tarea (Reasignación)
* **Contexto:** El Promotor necesita que un compañero o el Gerente realice una acción puntual (ej. "Validar descuento especial").
* **Acción:** En el campo **"Asignado a"**, borra su nombre y busca al usuario correspondiente (ej. Responsable de Ventas).
* **Resultado:** La tarea desaparece de la lista de trabajo del Promotor y aparece en la del usuario asignado. Salesforce envía una notificación al nuevo propietario.

### C. Validación de Datos (Excepción)
* **Disparador:** El Promotor intenta guardar una tarea sin **Asunto**.
* **Resultado:** Salesforce impide el guardado y resalta el campo con el error *"Complete este campo"*.

## 5. Diccionario de Datos (Campos de Tarea)

Estos son los campos estándar que utiliza este caso de uso:

| Campo (Label) | API Name | Tipo | Obligatorio | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| Asunto | `Subject` | Combo/Text | **Sí** | El título de la acción (Llamada, Email, etc.). |
| Fecha de Vencimiento | `ActivityDate` | Date | No* | *Recomendado* para activar recordatorios. |
| Asignado a | `OwnerId` | Lookup(User)| **Sí** | Quién debe realizar la tarea. |
| Estado | `Status` | Picklist | **Sí** | Valor inicial por defecto: "Not Started" (No iniciada). |
| Prioridad | `Priority` | Picklist | No | Normal / Alta. |
| Relacionado con | `WhatId` | Lookup | No | Vincula la tarea a la Oportunidad/Cuenta. |
| Nombre | `WhoId` | Lookup | No | Vincula la tarea al Contacto/Lead. |

## 6. Post-condiciones

1.  **Agenda:** La tarea aparece en la "Lista de Tareas" del Promotor (página de inicio o utilidad de barra inferior).
2.  **Notificaciones:** Si la configuración de Salesforce lo permite, el sistema enviará una notificación emergente o por correo electrónico la mañana del vencimiento de la tarea.
3.  **Reportes:** La Oportunidad deja de aparecer en reportes de "Oportunidades descuidadas" o "Sin actividad futura".

---

<img width="10024" height="4048" alt="CU-VE-007" src="https://github.com/user-attachments/assets/4774a241-1dc9-428d-ab0d-f806156cf193" />
