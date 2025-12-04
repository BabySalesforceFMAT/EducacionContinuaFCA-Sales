# Especificación de Caso de Uso: Gestionar Notificación de Recordatorio

**ID:** CU-VE-010  
**Actor(es):** Promotor de Ventas (Agente), Sistema (Automático)  
**Objeto Salesforce:** Task (Tarea) / Event (Evento)

---

## 1. Descripción
El Sistema notifica proactivamente al Promotor de Ventas sobre una tarea que requiere atención inmediata. Esto ocurre mediante una alerta en la campana de notificaciones cuando se cumple la fecha/hora programada o cuando una regla de automatización detecta inactividad (ej. "3 días sin contacto"). El agente debe decidir si atenderla al momento, posponerla o descartarla.

## 2. Precondiciones
1.  **Configuración:** El usuario tiene habilitados los recordatorios de actividad en su perfil personal.
2.  **Disparador:**
    * Existe una Tarea con la casilla `Reminder Set` (Recordatorio activado) marcada y la hora ha llegado.
    * O bien, un *Flow* automatizado ha creado una tarea de alerta por inactividad.
3.  **Sesión:** El usuario tiene Salesforce abierto en el navegador (o la App móvil).

## 3. Flujo Básico (Atender y Completar)

1.  **Disparo del Evento:** El Sistema detecta que la hora del recordatorio ha llegado.
2.  **Notificación Visual:**
    * Aparece la **Ventana de Recordatorios** (*Activity Reminders*) en la parte inferior (o superior) de la pantalla.
    * Suena una alerta acústica (si está configurado).
    * La ventana muestra: *Asunto*, *Fecha límite* y *Nombre del Lead/Oportunidad*.
3.  **Interacción:** El Promotor hace clic en el **Asunto** de la tarea dentro de la ventana emergente.
4.  **Redirección:** El sistema abre la ficha de la Tarea o la Oportunidad relacionada en una nueva pestaña o sub-pestaña de la consola.
5.  **Ejecución:** El Promotor realiza la acción requerida (ej. realiza la llamada telefónica).
6.  **Cierre:**
    * El Promotor marca el Estado de la tarea como **"Completed"**.
    * O bien, en la ventana de recordatorios, hace clic en **"Descartar"** (*Dismiss*) si ya realizó la acción sin entrar al registro.
7.  **Resultado:** La notificación desaparece de la lista de pendientes.

## 4. Flujos Alternativos

### A. Posponer Recordatorio (Snooze)
* **Contexto:** El Agente está ocupado en otra llamada y no puede atender la notificación ahora.
* **Acción:**
    1.  En la Ventana de Recordatorios, selecciona la tarea (o "Descartar todo/Posponer todo").
    2.  En el menú desplegable de tiempo, selecciona un intervalo: **"Recordármelo en 10 minutos"**, **"1 hora"**, **"Mañana"**.
    3.  Hace clic en **"Posponer"** (*Snooze*).
* **Resultado:** La notificación se oculta temporalmente y volverá a saltar cumplido el tiempo seleccionado.

### B. Notificación por Inactividad (Automatización)
* **Contexto:** El sistema detecta que una Oportunidad lleva 3 días sin cambios (Regla de Negocio).
* **Lógica del Sistema:**
    1.  Un proceso nocturno (*Scheduled Flow*) recorre las oportunidades abiertas.
    2.  Si `LastActivityDate` > 3 días, el sistema crea una nueva Tarea con Asunto: *"Alerta: Sin actividad reciente"* y activa el recordatorio inmediato.
* **Flujo del Usuario:** El usuario recibe la notificación como una tarea normal (Flujo Básico) y debe contactar al cliente para "reactivar" la oportunidad.

### C. Descartar sin Completar
* **Contexto:** El Agente ya habló con el cliente por otro medio y no necesita el recordatorio, pero quiere mantener la tarea abierta.
* **Acción:** En la ventana emergente, hace clic en la **"X"** o en **"Descartar"** (*Dismiss*).
* **Resultado:** La notificación visual desaparece, pero la tarea sigue existiendo en la base de datos con su estado original ("Not Started").

## 5. Datos Técnicos del Recordatorio

Para que este caso de uso funcione, los siguientes campos del objeto **Task** son críticos:

| Campo | API Name | Tipo | Función |
| :--- | :--- | :--- | :--- |
| Recordatorio activado | `IsReminderSet` | Checkbox | Debe ser `TRUE` para que salte la ventana. |
| Fecha/hora recordatorio| `ReminderDateTime`| DateTime | El momento exacto del disparo de la alerta. |
| Asunto | `Subject` | Text | El título que ve el usuario en el Pop-up. |
| Estado | `Status` | Picklist | Al cambiar a "Completed", el recordatorio se apaga. |

## 6. Post-condiciones

1.  **Gestión:** La alerta visual deja de molestar al usuario.
2.  **Registro:**
    * Si se completó: La tarea pasa al historial.
    * Si se pospuso: El campo `ReminderDateTime` se actualiza a la nueva hora futura.
    * Si se descartó: La tarea sigue pendiente pero sin alerta activa.

---

<img width="14642" height="4461" alt="CU-VE-010" src="https://github.com/user-attachments/assets/f1fd5987-75fb-4f83-8324-36b5dcc58bcb" />
