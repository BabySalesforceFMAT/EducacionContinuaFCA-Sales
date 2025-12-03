# Especificación de Caso de Uso: Registrar Interacción con Oportunidad

**ID:** CU-VE-006  
**Actor(es):** Promotor de Ventas (Agente)  
**Objeto Salesforce:** Opportunity (Oportunidad), Task/Event (Actividad)

---

## 1. Descripción
El Promotor de Ventas registra en la **Cronología de Actividades** (*Activity Timeline*) de la Oportunidad cualquier contacto, evento o hito relevante (ej. llamadas, mensajes de WhatsApp, correos o reuniones). Esto genera un historial centralizado que permite a cualquier usuario autorizado entender el contexto y el estado de la negociación sin necesidad de preguntar al propietario.

## 2. Precondiciones
1.  **Existencia:** Existe una Oportunidad abierta en el sistema.
2.  **Acceso:** El usuario tiene permisos de lectura/escritura sobre la Oportunidad.
3.  **Configuración:** Los tipos de actividades (Subjects) están configurados en el sistema (ej. Llamada, Email, WhatsApp).

## 3. Flujo Básico (Registrar una Actividad Pasada)

1.  **Navegación:** El Promotor accede a la ficha de la **Oportunidad**.
2.  **Panel de Actividad:** Localiza el componente de "Actividad" (generalmente a la derecha o en una pestaña central).
3.  **Selección de Acción:** Hace clic en la pestaña **"Registrar una llamada"** (*Log a Call*).
    * *Nota:* En Salesforce, esta función se usa genéricamente para registrar cualquier interacción completada, no solo llamadas telefónicas.
4.  **Captura de Datos:**
    * **Asunto (Subject):** Selecciona o escribe el tipo de interacción (ej. "WhatsApp enviado", "Llamada de seguimiento").
    * **Comentarios (Comments):** Escribe el detalle de la conversación (ej. "El cliente solicitó el plan de estudios y confirmó interés en el descuento de egresado").
    * **Relacionado con:** Verifica que esté vinculado a la Oportunidad y al Contacto correspondiente.
5.  **Guardar:** Hace clic en el botón **"Guardar"**.
6.  **Actualización:**
    * La interacción aparece inmediatamente en la **Cronología de Actividades** bajo "Actividades pasadas".
    * El campo `LastActivityDate` de la Oportunidad se actualiza automáticamente.

## 4. Flujos Alternativos

### A. Programar Seguimiento Futuro (Tarea)
* **Contexto:** El cliente pide que se le contacte la próxima semana.
* **Acción:**
    1.  En el panel de Actividad, selecciona **"Nueva Tarea"** (*New Task*).
    2.  Asigna un **Asunto** (ej. "Llamar para cerrar inscripción").
    3.  Define una **Fecha de Vencimiento** (*Due Date*).
    4.  Guarda.
* **Resultado:** La tarea queda pendiente en la sección "Próximos pasos" y Salesforce enviará un recordatorio al Agente en la fecha estipulada.

### B. Cerrar Oportunidad como Ganada (Venta Cerrada)
* **Contexto:** La interacción fue exitosa y el cliente confirma el pago.
* **Acción:**
    1.  El Agente registra la interacción (Flujo Básico) indicando el cierre.
    2.  Se dirige al campo **Etapa** (*Stage*) o a la barra de avance (*Path*).
    3.  Selecciona la etapa **"Closed Won"** (Cerrada Ganada).
    4.  Hace clic en **"Seleccionar estado cerrado"**.
* **Validación Crítica:** El sistema verifica las Reglas de Validación antes de guardar (ver Excepción A).

### C. Cerrar Oportunidad como Perdida
* **Contexto:** El cliente declina la oferta.
* **Acción:** Cambia la etapa a **"Closed Lost"** (Cerrada Perdida). El sistema suele solicitar un "Motivo de pérdida" (Loss Reason) si está configurado.

## 5. Excepciones y Reglas de Validación (Errores de Negocio)

### Excepción A: Productos No Confirmados
* **Disparador:** El Agente intenta cambiar la etapa a "Closed Won".
* **Condición:** El campo `Productos_Confirmados__c` está marcado como `FALSE`.
* **Error del Sistema:** Muestra el mensaje:
    > *"No se puede cerrar la oportunidad como ganada. Debe agregar los productos correspondientes y marcar la casilla 'Producto(s) Confirmado(s)' para continuar."*
* **Solución:** El Agente debe agregar los productos (Listas de Precios) y marcar la casilla de verificación requerida.

### Excepción B: Promoción No Validada
* **Disparador:** El Agente intenta cambiar la etapa a "Closed Won".
* **Condición:** El campo `Promocion_Validada__c` está marcado como `FALSE`.
* **Error del Sistema:** Muestra el mensaje:
    > *"No puedes avanzar a la etapa 'Cerrada Ganada' sin antes validar la promoción. Por favor, marca la casilla 'Validar Promoción' si ya realizaste este paso."*
* **Solución:** El Agente debe verificar que los documentos de descuento sean correctos y marcar la casilla.

## 6. Datos Relevantes en la Interacción

| Campo | Objeto | Descripción |
| :--- | :--- | :--- |
| Asunto | Task/Event | Título breve de la interacción (ej. "Envío de Cotización"). |
| Comentarios | Task/Event | Detalle narrativo de lo sucedido. |
| Fecha de Vencimiento | Task | Para seguimientos futuros. |
| Etapa (Stage) | Opportunity | Estado vital de la venta (Prospecting -> Closed Won). |
| Productos Confirmados | Opportunity | **Check (Custom).** Obligatorio para ganar la venta. |
| Promoción Validada | Opportunity | **Check (Custom).** Obligatorio para ganar la venta. |

## 7. Post-condiciones

1.  **Historial:** La bitácora de la oportunidad refleja la nueva interacción con fecha y autor.
2.  **Estado:** Si hubo cambio de etapa, la oportunidad avanza en el embudo de ventas o se cierra definitivamente.
3.  **Reportes:** La actividad se contabiliza en los reportes de rendimiento del Agente (ej. "Llamadas realizadas esta semana").
