# Manual de Operación: Portal de Ventas Salesforce

Este documento detalla el flujo de trabajo, roles y uso de la solución de ventas implementada en Salesforce para la gestión de cursos e inscripciones.

## 1. Roles y Perfiles
El sistema está diseñado para interactuar principalmente con dos perfiles de usuario:

* **Gerente de Ventas:** Responsable de la supervisión de la cola de prospectos y la asignación de leads a los agentes. Tiene permisos para reasignar propiedad de registros.
* **Agente de Ventas:** Responsable del seguimiento directo con el cliente, desde el primer contacto hasta el cierre de la venta (inscripción).

---

## 2. Gestión de Prospectos (Leads)

El ciclo de vida comienza cuando un interesado ingresa al sistema como un **Lead (Candidato)**.

### Flujo de Asignación
1.  **Creación:** El Lead se crea y entra automáticamente a la **"Cola de Prospectos sin Asignar"**.
2.  **Asignación:**
    * El **Gerente de Ventas** monitorea esta cola.
    * Selecciona un Lead y cambia el *Propietario del registro (Owner)* a un **Agente de Ventas** específico para su atención.

### Etapas del Lead (Lead Path)
El Agente de Ventas debe guiar al candidato a través de las siguientes etapas visuales:

1.  **Contactar:** Primer intento de comunicación.
2.  **Confirmación de Disponibilidad:** El agente verifica que el candidato tiene tiempo/interés real.
3.  **Solicitar Llenado Formato:** Se envía o solicita la información de inscripción.
4.  **Verificar Llenado de Solicitud:** El agente revisa que los datos sean correctos.
5.  **Convertido:** Si el cliente es viable, se presiona el botón **"Convert"**. Esto transforma el Lead en una **Cuenta**, un **Contacto** y una **Oportunidad**.

**Campos Clave en Lead:**
* *Medio de Contacto:* Origen del lead.
* *Estudio en Uady:* Checkbox informativo.
* *Descripción Cursos de Interés:* Campo de texto para notas cualitativas.

---

## 3. Gestión de Oportunidades (Ventas)

Una vez convertido, el proceso de venta formal (inscripción) se gestiona en el objeto **Opportunity**.

### Etapas de la Oportunidad (Opportunity Path)
El flujo de ventas sigue una validación estricta por fases:

1.  **Verificar Documentos Para...:** Revisión inicial de requisitos.
2.  **Verificación Fiscal y Validación:**
    * Se determina si el cliente requiere facturación.
    * *Campo:* `Necesita Factura` (Checkbox).
3.  **Cálculo y Autorización De Precios:**
    * Etapa crítica para aplicar descuentos y confirmar montos.
    * *Campo:* `Tipo De Descuento` (Lista de selección).
    * *Campo:* `Descuento Aprobado` (Checkbox - solo lectura/aprobación).
4.  **Gestión de Pago y Facturación:**
    * Registro de la transacción financiera.
    * *Campo:* `Comprobante De Pago Adj` (Checkbox).
5.  **Creación de Orden:** Generación del pedido final.
6.  **Closed (Cerrada Ganada):** Inscripción completada exitosamente.

---

## 4. Productos y Cursos
La solución utiliza el objeto estándar **Products (Productos)** para representar la oferta educativa.

* **Uso:** En la lista relacionada "Products" dentro de la Oportunidad, el agente debe agregar los cursos a los que el cliente se inscribe.
* **Ejemplos de configuración:**
    * *Producto:* Contaduría | *Precio:* $800.00
    * *Producto:* Finanzas Personales | *Precio:* $9,000.00
* **Impacto:** El monto total de la Oportunidad se calcula automáticamente sumando los productos agregados ("Amount").

---

## 5. Gestión Documental y Aprobaciones
Para mantener la integridad de los datos, se utiliza un sistema de validación de documentos mediante listas relacionadas y checkboxes de control.

### Proceso de Aprobación de Documentos
Para avanzar en etapas críticas (como Descuentos o Pagos), el Agente debe seguir este flujo:

1.  **Carga de Archivo:**
    * Ir a la lista relacionada **"Files" (Archivos)** en la parte derecha de la pantalla.
    * Clic en *Upload Files* y subir el comprobante (ej. PDF del comprobante de pago o constancia para descuento).
2.  **Validación en Registro:**
    * Una vez cargado el archivo, el agente debe marcar el checkbox correspondiente en la sección de detalles para indicar que el documento existe.
    * *Ejemplo:* Marcar `Documentos Comprobatorios Descuento Adj` o `Comprobante De Pago Adj`.
3.  **Revisión (Gerencia):**
    * El Gerente revisa el archivo en la lista "Files".
    * Si es correcto, procede a validar la etapa o autorizar el descuento marcando `Descuento Aprobado` o `Productos Confirmados`.

---
