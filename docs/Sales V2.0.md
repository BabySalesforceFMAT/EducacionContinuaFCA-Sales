# Manual de Operación Detallado: Portal de Ventas y Gestión Educativa

**Versión:** 2.0  
**Plataforma:** Salesforce Lightning Experience  
**Alcance:** Gestión del ciclo de vida del alumno (Lead to Cash), validaciones académicas y cierre de ventas.

---

## 1. Definición de Roles y Modelo de Seguridad

El sistema opera bajo un modelo de segregación de funciones para asegurar la integridad de los datos financieros y académicos.

### 1.1. Gerente de Ventas (Perfil Aprobador)
* **Responsabilidad:** Supervisión de la cola de prospectos y auditoría de calidad.
* **Permisos Exclusivos:**
    * Es el único perfil autorizado para marcar campos de **Validación Final** (ej. `Comprobacion_De_Descuento_Aprobada__c`).
    * Capacidad de reasignar la propiedad de registros (*Change Owner*) en casos de ausencia de agentes.

### 1.2. Agente de Ventas (Perfil Operativo)
* **Responsabilidad:** Contacto directo con el alumno, recolección de documentos y gestión de la inscripción.
* **Limitaciones del Sistema:**
    * Puede subir archivos y solicitar validaciones.
    * **No tiene permisos de escritura** en campos de aprobación gerencial (estos campos aparecen como "Solo Lectura").

---

## 2. Gestión Granular de Prospectos (Leads)

El objeto **Lead** actúa como un área de "pre-inscripción". El objetivo es calificar el interés académico y recolectar la documentación inicial antes de generar una Oportunidad de venta.

### 2.1 Flujo de Asignación Automática
1.  **Ingreso:** Al crearse un Lead (manualmente o vía web), este ingresa a la **"Cola de Prospectos sin Asignar"**.
2.  **Distribución:** El Gerente monitorea la cola y asigna el Lead a un Agente específico cambiando el campo `OwnerId`.

### 2.2 Captura de Datos Académicos (Perfilado)
Para calificar correctamente al prospecto, el Agente debe completar los siguientes campos obligatorios personalizados:

* **Evento de Interés (`EventoEC__c`):**
    * *Tipo:* Lookup (Búsqueda).
    * *Acción:* El agente debe buscar en el catálogo el curso o diplomado específico que interesa al alumno.
* **Descripción Cualitativa (`Descripci_n_Cursos_De_Inter_s__c`):**
    * *Tipo:* Texto Largo.
    * *Uso:* Espacio para notas detalladas sobre las necesidades educativas del prospecto.
* **Detección de Promociones:**
    * Si el prospecto es ex-alumno, se debe marcar el checkbox **`Estudio en Uady`**.
    * Si cuenta con certificado previo, marcar **`Certificado_De_Lealtad__c`**.
    * Seleccionar la promoción aplicable en la lista: **`Tipo_de_Promocion__c`** (Valores: *Egresado Uady, Certificado De Lealtad, Ninguna*).

### 2.3 Proceso de Conversión y Validación (Candado del Sistema)
El sistema impide la conversión de prospectos "basura" mediante una **Regla de Validación estricta**.

> **🛑 Regla de Validación:** `Requerir_Formato_Inscripcion_Validado`
>
> * **Condición:** No se permite convertir si el campo `Formato_De_Inscripcion_Validado__c` está vacío (FALSE).
> * **Mensaje de Error:** *"No puedes convertir este prospecto. El 'Formato de Inscripción' aún no ha sido marcado como validado."*
> * **Acción Correctiva:** El Agente debe recibir el formato de inscripción firmado por el alumno, subirlo a *Files* y marcar manualmente la casilla de verificación antes de presionar "Convertir".

---

## 3. Gestión de Oportunidades (Ciclo de Inscripción)

Al convertir el Lead, se genera una **Opportunity**. Aquí ocurre la transacción formal. El flujo está controlado por etapas que requieren validaciones de "Doble Check" (Agente solicita -> Gerente aprueba).

### 3.1 Configuración de la Oferta (Productos)
El monto de la venta (`Amount`) está bloqueado para edición manual. Se calcula automáticamente sumando los productos.
* **Paso Operativo:** Ir a la lista relacionada **Products** -> *Add Product*.
* **Selección:** Buscar el curso vinculado anteriormente. El precio base se cargará desde la lista de precios estándar.

### 3.2 Sub-proceso: Aprobación de Descuentos
Si el alumno aplica para un descuento, se ejecuta el siguiente flujo lógico:

1.  **Solicitud:** El Agente selecciona el motivo en `Tipo_De_Descuento__c`.
2.  **Evidencia:** Sube el comprobante (Kardex/Credencial) a la sección *Files*.
3.  **Check de Agente:** Marca **`Documentos_Comprobatorios_Descuento_Adj__c`** para confirmar la carga.
4.  **Aprobación Gerencial:** El Gerente valida la evidencia y marca el campo **`Comprobacion_De_Descuento_Aprobada__c`**.
    * *Nota:* Sin este segundo check, no se autoriza la facturación con descuento.

### 3.3 Sub-proceso: Facturación y Cobro
1.  **Requisición:** Si el alumno requiere factura, marcar `Necesita_Factura__c`.
2.  **Carga de Pago:** El Agente sube el voucher bancario a *Files*.
3.  **Notificación:** El Agente marca **`Comprobante_De_Pago_Adj__c`**.
4.  **Conciliación:** El área administrativa verifica el ingreso en banco y marca **`Comprobante_De_Pago_Aprobado__c`**.

### 3.4 Cierre de Venta (Validaciones Finales)
Para cambiar la etapa a **"Closed Won"** (Inscrito), el sistema evalúa dos reglas de validación simultáneas:

1.  **Validación de Producto:**
    * **Regla:** `Bloqueo_Productos_Confirmados`.
    * **Lógica:** Impide cerrar si `Productos_Confirmados__c` es Falso.
    * **Objetivo:** Asegurar que el alumno se inscribió al curso correcto antes de generar la orden.
2.  **Validación de Promoción:**
    * **Regla:** `Bloqueo_Validar_Promocion`.
    * **Lógica:** Si hay descuento, impide cerrar si `Promocion_Validada__c` es Falso.

---

## 4. Detalle del Catálogo de Productos (Product2)

El objeto Producto ha sido extendido para soportar la lógica de Educación Continua. Al consultar un curso, los usuarios visualizarán:

| Campo | API Name | Descripción Funcional |
| :--- | :--- | :--- |
| **Modalidad** | `Modalidad_de_Imparticion__c` | Indica si es Presencial, En línea o Híbrida. Vital para informar al alumno. |
| **Duración** | `Duracion_en_horas__c` | Número entero. Usado para constancias. |
| **Estructura** | `Estructura__c` | Define si es "Tema Único" o "Modular". |
| **Asistencia** | `Requisito_de_Asistencia__c` | Porcentaje mínimo (ej. 80%) para acreditar. |

---

## 5. Gestión Documental (Files & Attachments)

Para mantener la auditoría, el uso del componente **Salesforce Files** es obligatorio en dos momentos críticos:

1.  **Evidencia de Descuento:** Documento que avale el status de ex-alumno o convenio.
2.  **Evidencia de Pago:** Voucher, transferencia o comprobante de depósito.

**Importante:** El sistema no detecta automáticamente la subida de un archivo. Es responsabilidad del usuario marcar los checkboxes con sufijo `_Adj__c` (Adjunto) para notificar al sistema que el documento ya reside en la nube.

---

## 6. Matriz de Resolución de Errores (Troubleshooting)

Guía rápida para mensajes de error comunes basados en las Validation Rules activas.

| Mensaje en Pantalla | Causa Técnica | Solución Operativa |
| :--- | :--- | :--- |
| *"No puedes convertir este prospecto..."* | Regla `Requerir_Formato_Inscripcion_Validado` activada. | Verificar que el alumno entregó el formato y marcar el checkbox en el Lead. |
| *"No se puede cerrar la oportunidad como ganada. Debe agregar los productos..."* | Regla `Bloqueo_Productos_Confirmados` activada. | Revisar lista de productos y marcar el check de confirmación en la Oportunidad. |
| *"No puedes avanzar a 'Cerrada Ganada' sin validar la promoción"* | Regla `Bloqueo_Validar_Promocion` activada. | Solicitar al Gerente que valide la promoción aplicada o marcar el campo si se tienen permisos. |
