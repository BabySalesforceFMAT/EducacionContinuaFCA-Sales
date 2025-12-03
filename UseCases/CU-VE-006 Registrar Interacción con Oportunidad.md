# Especificación de Caso de Uso: Registrar Interacción con Oportunidad

**ID:** CU-VE-006  
**Fecha de última actualización:** 03/12/2025  
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

## 4. Post-condiciones
* El historial refleja la gestión del vendedor.
* No se generan recordatorios (porque es algo que ya pasó).

## 5. Datos Relevantes en la Interacción

| Campo | Objeto | Descripción |
| :--- | :--- | :--- |
| Asunto | Task/Event | Título breve de la interacción (ej. "Envío de Cotización"). |
| Comentarios | Task/Event | Detalle narrativo de lo sucedido. |
| Fecha de Vencimiento | Task | Para seguimientos futuros. |
| Etapa (Stage) | Opportunity | Estado vital de la venta (Prospecting -> Closed Won). |

## 6. Post-condiciones

1.  **Historial:** La bitácora de la oportunidad refleja la nueva interacción con fecha y autor.
2.  **Estado:** Si hubo cambio de etapa, la oportunidad avanza en el embudo de ventas o se cierra definitivamente.
3.  **Reportes:** La actividad se contabiliza en los reportes de rendimiento del Agente.

---

<img width="5666" height="3133" alt="CU-VE-006" src="https://github.com/user-attachments/assets/00ecbb46-2e94-44de-ab66-a72bfaf77ad0" />
