# Especificación de Caso de Uso: Consultar Estado del Pago

**ID:** CU-VE-012    
**Actor(es):** Promotor de Ventas (Agente)  
**Objeto Salesforce:** Opportunity (Oportunidad)

---

## 1. Descripción
El Promotor de Ventas consulta la ficha de la Oportunidad para verificar si el pago realizado por el cliente ha sido validado por el área de Contabilidad/Finanzas. Dado que el Promotor no tiene permisos para aprobar pagos, depende de la actualización de campos específicos ("Testigos") para saber si la venta puede cerrarse exitosamente o si el pago sigue pendiente/rechazado.

## 2. Precondiciones
1.  **Existencia:** La Oportunidad existe.
2.  **Contexto:** El cliente ya envió su comprobante y este fue cargado previamente (ver CU-VE-011).
3.  **Permisos:** El usuario tiene acceso de lectura a los campos de validación financiera.

## 3. Flujo Básico (Consulta Individual)

1.  **Navegación:** El Promotor accede a la ficha de la Oportunidad.
2.  **Revisión de Sección Financiera:** Localiza la sección de "Validación de Pagos" (o Detalles) en el diseño de página.
3.  **Interpretación de Campos (Lógica de Estado):** El sistema no muestra un semáforo explícito, por lo que el Promotor verifica el estado de las casillas:

    * **Estado: EN REVISIÓN (Por Registrar)**
        * *Indicador Visual:*
            * `Comprobante_De_Pago_Adj__c` = **TRUE** (El archivo está subido).
            * `Comprobante_De_Pago_Aprobado__c` = **FALSE** (Contabilidad aún no valida).
        * *Acción:* Esperar o contactar a Finanzas si demora mucho.

    * **Estado: CONFIRMADO (Éxito)**
        * *Indicador Visual:*
            * `Comprobante_De_Pago_Aprobado__c` = **TRUE**.
        * *Acción:* El Promotor procede a cerrar la oportunidad como "Ganada" (Closed Won).

    * **Estado: PENDIENTE DE PAGO (Sin acción)**
        * *Indicador Visual:* Ambas casillas están en **FALSE**.
        * *Acción:* Realizar labor de cobranza.

4.  **Verificación de Facturación (Opcional):**
    * Si el proceso requiere factura, el Promotor revisa el historial de actividad (Chatter) o notas para ver si Contabilidad solicitó datos fiscales adicionales antes de marcar el aprobado.

## 4. Flujos Alternativos

### A. Consulta Masiva (Vista de Lista)
* **Contexto:** El Promotor quiere ver a quiénes *ya* les aprobaron el pago hoy para cerrar las ventas.
* **Acción:**
    1.  Ingresa a la pestaña **Oportunidades**.
    2.  Selecciona una Vista de Lista creada previamente llamada **"Pagos Aprobados - Pendientes de Cierre"**.
    3.  Esta lista filtra registros donde `Comprobante_De_Pago_Aprobado__c` = TRUE y la Etapa no es "Closed Won".
* **Resultado:** Visualiza rápidamente qué clientes ya pagaron y valida el cierre en lote.

### B. Detección de Pago Vencido
* **Lógica del Sistema:**
    * El Promotor observa el campo **Fecha de Cierre** (`CloseDate`).
    * Si `CloseDate` < [Fecha de Hoy] Y `Comprobante_De_Pago_Aprobado__c` = FALSE.
    * **Interpretación:** El pago está vencido. El Promotor debe contactar al cliente para reactivar la fecha o marcar la oportunidad como "Perdida".

## 5. Diccionario de Datos (Campos de Estado)

Basado en `Opportunity - Fields.csv`, estos son los campos que determinan el estado del pago:

| Campo (Label) | API Name | Tipo | Rol en el Proceso |
| :--- | :--- | :--- | :--- |
| Comprobante Pago Adj | `Comprobante_De_Pago_Adj__c` | Checkbox | Indica que el cliente *dice* que ya pagó y envió evidencia. |
| Comprobante Pago Aprobado | `Comprobante_De_Pago_Aprobado__c` | Checkbox | **Crítico.** Indica que Finanzas *confirmó* que el dinero está en el banco. |
| Fecha de Cierre | `CloseDate` | Date | Fecha límite para recibir el pago. |
| Etapa | `StageName` | Picklist | El estado final de la venta. |

## 6. Post-condiciones

1.  **Información:** El Promotor conoce el estatus real de la transacción.
2.  **Toma de Decisión:**
    * Si está **Aprobado**: Ejecuta el caso de uso **CU-VE-008: Cerrar Oportunidad**.
    * Si está **Vencido**: Reprograma tarea de seguimiento o cierra como perdida.

---

<img width="10210" height="4637" alt="CU-VE-012" src="https://github.com/user-attachments/assets/d2ad613c-7d83-45c2-983a-2ed87821d0b1" />
