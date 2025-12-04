# Especificación de Caso de Uso: Cargar Documentación

**ID:** CU-VE-011  
**Actor(es):** Promotor de Ventas / Staff Administrativo  
**Objeto Salesforce:** Opportunity (Oportunidad) + ContentDocument (Archivos)

---

## 1. Descripción
El personal de staff carga los documentos comprobatorios (identificación, comprobante de estudios o pago) directamente en la Oportunidad en nombre del cliente. Esto es necesario cuando el cliente envía los archivos por medios externos (WhatsApp, Email) y no por el portal de autoservicio.

El proceso implica dos acciones en Salesforce:
1.  Subir el archivo digital al registro.
2.  Actualizar los "testigos" (Checkboxes) en la Oportunidad para indicar que la documentación ya fue entregada y está lista para revisión.

## 2. Precondiciones
1.  **Existencia:** Existe una Oportunidad activa en el sistema.
2.  **Material:** El Promotor tiene el archivo digital (PDF, JPG, PNG) en su computadora.
3.  **Permisos:** El usuario tiene permisos de edición sobre la Oportunidad y acceso a la lista relacionada de "Archivos" (*Files*).

## 3. Flujo Básico (Carga y Actualización de Estado)

1.  **Navegación:** El Promotor accede a la ficha de la **Oportunidad** del cliente.
2.  **Carga de Archivo:**
    * Se dirige a la lista relacionada o componente **"Archivos"** (*Files*).
    * Hace clic en **"Agregar archivos"** o **"Cargar archivos"**.
    * Selecciona el documento desde su ordenador (ej. "Titulo_JuanPerez.pdf").
    * Hace clic en **"Listo"** una vez finalizada la carga.
3.  **Actualización de Testigos:**
    * Una vez subido el archivo, el Promotor debe editar el registro de la Oportunidad.
    * Localiza la sección de validación (según tus campos personalizados).
    * Marca la casilla correspondiente al documento subido:
        * Si es comprobante de pago: Marca **`Comprobante_De_Pago_Adj__c`**.
        * Si es documento de descuento: Marca **`Documentos_Comprobatorios_Descuento_Adj__c`**.
4.  **Guardar:** Hace clic en **"Guardar"**.
5.  **Resultado:**
    * El archivo queda almacenado de forma segura vinculado a la Oportunidad.
    * Al marcar las casillas `_Adj__c` (Adjunto), el sistema interpreta que la documentación está en estado **"Pendiente de Validación"** (lista para que un supervisor la revise).

## 4. Flujos Alternativos

### A. Reemplazo de Documento
* **Contexto:** El cliente envió una foto borrosa y ahora envió una mejor.
* **Acción:**
    1.  En la lista de "Archivos", el Promotor hace clic en la flecha al lado del archivo antiguo.
    2.  Selecciona **"Cargar nueva versión"**.
    3.  Sube el nuevo archivo.
* **Ventaja:** Salesforce mantiene el historial de versiones, pero muestra siempre la más reciente, evitando duplicidad de archivos basura.

### B. Múltiples Documentos a la vez
* **Contexto:** El cliente envió INE y Título en el mismo correo.
* **Acción:** En el paso de carga, el Promotor selecciona ambos archivos simultáneamente. Luego marca ambas casillas (`Documentos...Adj` y `Comprobante...Adj` si aplica) antes de guardar.

### C. Error de Formato
* **Disparador:** El usuario intenta subir un archivo `.exe` o un archivo superior al límite de la organización (generalmente 2GB).
* **Resultado:** Salesforce muestra una alerta roja: *"No se puede cargar el archivo. Tipo de archivo no permitido o excede el límite"*.

## 5. Diccionario de Datos (Campos Afectados)

| Campo (Label) | API Name | Tipo | Función en el Flujo |
| :--- | :--- | :--- | :--- |
| Comprobante De Pago Adj | `Comprobante_De_Pago_Adj__c` | Checkbox | **Output.** Se marca TRUE manualmente tras subir el pago. Indica "Listo para revisión". |
| Docs. Comprobatorios Desc. Adj | `Documentos_Comprobatorios_Descuento_Adj__c` | Checkbox | **Output.** Se marca TRUE tras subir título/INE. Habilita la revisión de descuentos. |
| Comprobante Pago Aprobado | `Comprobante_De_Pago_Aprobado__c` | Checkbox | **Lectura.** El Promotor NO marca esto; espera que el Validador lo haga después. |
| Comprobación Descuento Aprobada| `Comprobacion_De_Descuento_Aprobada__c`| Checkbox | **Lectura.** Estado final de la validación. |
| Tipo De Descuento | `Tipo_De_Descuento__c` | Picklist | Contexto. Define qué documentos se esperan (ej. si es "Egresado UADY", se espera el título). |

## 6. Post-condiciones

1.  **Archivos:** Los documentos digitales residen en el objeto `ContentDocumentLink` vinculados a la `Opportunity`.
2.  **Estado del Proceso:**
    * La Oportunidad ahora tiene las casillas `..._Adj__c` en `TRUE`.
    * Esto cumple parcialmente los requisitos para futuras validaciones (recordando las reglas de validación de cierre de oportunidad que vimos en el CU anterior).

---

<img width="17186" height="4643" alt="CU-VE-011" src="https://github.com/user-attachments/assets/099056fa-bc25-4064-9673-902635dbdad7" />
