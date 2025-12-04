# Especificación de Caso de Uso: Recuperar Leads de Fuentes Externas

**ID:** CU-VE-001    
**Actor(es):** Sistema (Automático), Administrador de Marketing / Ventas  
**Objeto Salesforce:** Lead (Prospecto)

---

## 1. Descripción
Este proceso describe cómo el sistema "recupera" o ingesta información de interesados proveniente de canales externos (Landing Pages, Campañas de Facebook/Instagram Ads, Formularios en sitio web o Bases de datos compradas) y la convierte automáticamente en registros de **Lead** dentro de Salesforce.

El objetivo es centralizar la información dispersa en una única base de datos para que los Promotores de Ventas puedan iniciar la gestión (CU-VE-002 en adelante).

## 2. Precondiciones
1.  **Configuración Técnica:** La funcionalidad *Web-to-Lead* está activa o se cuenta con permisos para usar el *Data Import Wizard*.
2.  **Mapeo de Datos:** Los campos del formulario externo coinciden con los campos (API Names) de Salesforce definidos en el diccionario de datos.
3.  **Integridad:** La fuente externa debe proveer al menos los **Apellidos** (`LastName`) y la **Compañía** (`Company`), ya que son obligatorios en tu configuración de Salesforce.

## 3. Flujo Básico (Automático: Web-to-Lead / API)

1.  **Captura Externa:** Un interesado llena un formulario en una página web externa (ej. "Solicitar informes diplomado").
2.  **Transmisión:** El sistema externo envía los datos a Salesforce mediante el protocolo estándar (HTTPS POST).
3.  **Recepción y Mapeo:** Salesforce recibe la data y la asigna a los campos correspondientes según tu definición de objetos:
    * El campo "Nombre" externo → `FirstName`.
    * El campo "Apellidos" externo → `LastName`.
    * El campo "Origen" externo → **`Medio_de_Contacto__c`** (Campo Custom).
    * El campo "Interés (Texto)" externo → **`Descripci_n_Cursos_De_Inter_s__c`**.
4.  **Validación de Duplicados:** Salesforce verifica si el correo electrónico (`Email`) ya existe en la base de datos.
    * *Si no existe:* Crea un nuevo registro.
    * *Si existe:* (Dependiendo de la regla de duplicados activa) puede bloquear la creación o crear una alerta en el registro existente.
5.  **Asignación Automática:**
    * El sistema ejecuta las **Reglas de Asignación de Leads** (*Lead Assignment Rules*).
    * Basado en criterios (ej. Si `Medio_de_Contacto__c` contiene "Facebook"), asigna el Lead a una "Cola" (*Queue*) o a un Promotor específico.
6.  **Respuesta Automática (Opcional):** El sistema envía un correo de "Gracias por tu interés" al prospecto usando las *Auto-Response Rules*.

## 4. Flujo Alternativo (Manual: Importación Masiva)

* **Contexto:** El equipo de Marketing tiene un archivo Excel con 500 interesados recolectados en una feria presencial o evento.
* **Actor:** Administrador / Marketing.
* **Pasos:**
    1.  El actor limpia el archivo CSV externo para asegurar calidad.
    2.  Ingresa a Salesforce y abre el **Asistente de Importación de Datos** (*Data Import Wizard*).
    3.  Selecciona el objeto **Prospectos (Leads)**.
    4.  Carga el archivo y mapea las columnas del Excel a los campos de Salesforce (ver Diccionario abajo).
    5.  Ejecuta la importación.
    6.  El sistema procesa los registros y reporta cuántos se crearon exitosamente y cuántos fallaron.

## 5. Excepciones y Manejo de Errores

### A. Falta de Campos Obligatorios
* **Situación:** La fuente externa envía un lead sin `Company` (Compañía) o sin `LastName`.
* **Resultado:** Salesforce rechaza la creación del registro.
* **Acción del Sistema:** Genera un registro de error (Log) que debe ser revisado por el Administrador.
    * *Solución Técnica:* Configurar el formulario web para enviar un valor por defecto (ej. "Particular" o "Sin Especificar") en el campo `Company` para prospectos B2C.

### B. Error en Campo de Lista (Picklist)
* **Situación:** El formulario externo envía en el campo `Tipo_de_Promocion__c` un valor no válido (ej. "Descuento Verano"), cuando Salesforce solo acepta "Egresado Uady", "Certificado De Lealtad", "Ninguna".
* **Resultado:** Si la lista de selección está restringida, la importación del campo falla o queda vacío.

## 6. Diccionario de Datos para Integración

Para que este caso de uso funcione, los desarrolladores web o el equipo de marketing deben mapear sus formularios a estos API Names exactos (extraídos de tus archivos):

| Campo Salesforce | API Name | Tipo | Obligatorio | Nota para Integración |
| :--- | :--- | :--- | :--- | :--- |
| Apellidos | `LastName` | Text | **SÍ** | Sin esto falla la carga. |
| Compañía | `Company` | Text | **SÍ** | En B2C enviar valor genérico. |
| Correo | `Email` | Email | No | Clave para detectar duplicados. |
| Medio de Contacto | `Medio_de_Contacto__c` | Text(100) | No | Enviar aquí la fuente (ej. "Facebook Ads"). |
| Desc. Interés | `Descripci_n_Cursos_De_Inter_s__c` | Long Text | No | Usar para capturar el interés inicial (Texto). |
| Tipo de Promoción | `Tipo_de_Promocion__c` | Picklist | No | Validar valores exactos con el CSV. |

## 7. Post-condiciones

1.  **Base de Datos:** Los nuevos registros aparecen en la tabla de Leads.
2.  **Visibilidad:**
    * Si se asignaron correctamente, aparecen en la vista "Sin Asignar" o en la lista del Promotor correspondiente.
    * Están listos para ser procesados por el caso de uso **CU-VE-003 (Visualizar Leads)**.

---

<img width="11657" height="4322" alt="CU-VE-001" src="https://github.com/user-attachments/assets/0e1fcefa-c0ee-4411-8409-c83dfff064c6" />
