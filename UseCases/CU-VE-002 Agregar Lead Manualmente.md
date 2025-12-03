# Especificación de Caso de Uso: Agregar Lead Manualmente

**ID:** CU-VE-002  
**Actor(es):** Promotor de Ventas / Agente de Ventas  
**Objeto Salesforce:** Lead (Prospecto)

---

## 1. Descripción
El Promotor de Ventas crea manualmente un nuevo registro en el objeto **Lead** dentro de Salesforce. Esto ocurre cuando un interesado contacta por un canal no integrado automáticamente (ej. WhatsApp directo, llamada telefónica o correo personal) y se requiere registrar su información, interés académico y medio de contacto para iniciar el seguimiento.

## 2. Precondiciones
1.  **Acceso:** El usuario tiene una licencia activa de Salesforce y el perfil adecuado (ej. "Agente de Ventas").
2.  **Permisos:** El usuario tiene permisos de **Crear** y **Editar** sobre el objeto `Lead`.
3.  **Datos:** El usuario dispone de la información mínima requerida del prospecto (Apellidos y Compañía).

## 3. Flujo Básico (Camino Feliz)

1.  **Navegación:** El Promotor accede a la aplicación de Ventas y selecciona la pestaña **Prospectos (Leads)**.
2.  **Iniciar Creación:** Hace clic en el botón **"Nuevo"** situado en la vista de lista.
3.  **Selección de Tipo de Registro:** (Si aplica) Selecciona el *Record Type* correspondiente a la línea de negocio (ej. "Educación Continua").
4.  **Captura de Datos:** El sistema despliega el formulario (*Page Layout*). El Promotor ingresa la información mapeando los datos a los campos de la organización:

    * **Información de Contacto:**
        * `First Name` (Nombre): Nombre de pila del interesado.
        * `Last Name` (Apellidos): **[Obligatorio]** Apellidos del interesado.
        * `Email`: Correo electrónico principal.
        * `Phone` / `Mobile`: Número de contacto.

    * **Información de Negocio:**
        * `Medio_de_Contacto__c` (Texto): Ingresa manualmente la fuente (ej. "WhatsApp", "Referido").
        * `EventoEC__c` (Lookup): Busca y selecciona el Grupo o Evento de Educación Continua de interés.
        * `Descripci_n_Cursos_De_Inter_s__c` (Texto Largo): Agrega detalles adicionales sobre lo que busca el cliente.
        * `Tipo_de_Promocion__c` (Lista de Selección): Selecciona si aplica alguna promoción especial (Valores: *Egresado Uady*, *Certificado De Lealtad*, *Ninguna*).
        * `Certificado_De_Lealtad__c` (Casilla): Se marca si el cliente presenta dicho certificado.

5.  **Guardar:** El Promotor hace clic en el botón **"Guardar"**.
6.  **Validaciones del Sistema:**
    * Salesforce verifica que los campos `Last Name` y `Name` no estén vacíos.
    * Se ejecutan las Reglas de Duplicados para verificar si el email ya existe.
7.  **Confirmación:** El sistema crea el registro, asigna un `Lead ID` único y muestra una notificación de éxito ("Prospecto creado").
8.  **Redirección:** El usuario es llevado a la vista de detalle del nuevo Prospecto.

## 4. Flujos Alternativos y Excepciones

### A. Detección de Duplicados
* **Disparador:** Al guardar, el sistema detecta que el `Email` coincide con un registro existente.
* **Acción:** Salesforce muestra una alerta: *"Parece que existe un duplicado..."* y habilita la opción **Ver Duplicados**.
* **Resolución:** El Promotor revisa la coincidencia y decide si actualizar el registro existente o forzar la creación del nuevo (si sus permisos lo permiten).

### B. Campos Obligatorios Faltantes
* **Disparador:** El Promotor intenta guardar sin llenar `Last Name` o `Name`.
* **Acción:** El sistema detiene el proceso y resalta los campos faltantes en rojo con el mensaje *"Este campo es obligatorio"*.
* **Resolución:** El Promotor completa la información y vuelve a guardar.

### C. Nota sobre Conversión Futura (Regla de Validación)
* *Contexto:* Existe una regla de validación activa llamada `Requerir_Formato_Inscripcion_Validado`.
* **Comportamiento:** Esta regla **NO** bloquea la creación del Lead (este caso de uso), pero el Promotor debe saber que el campo `Formato_De_Inscripcion_Validado__c` deberá ser marcado como `TRUE` antes de intentar convertir el Lead en una Oportunidad en el futuro.

## 5. Diccionario de Datos Relevante

| Campo (Label) | API Name | Tipo | Obligatorio | Descripción / Notas |
| :--- | :--- | :--- | :--- | :--- |
| Apellidos | `LastName` | Text(80) | **Sí** | Campo estándar. |
| Medio de Contacto | `Medio_de_Contacto__c` | Text(100) | No | Entrada manual de la fuente del lead. |
| Evento de Interés | `EventoEC__c` | Lookup | No | Relación con el objeto Grupo/Evento. |
| Tipo de Promoción | `Tipo_de_Promocion__c` | Picklist | No | Valores: Egresado Uady, Certificado Lealtad, Ninguna. |
| Desc. Cursos Interés| `Descripci_n_Cursos_De_Inter_s__c`| Long Text | No | Detalles específicos del interés. |
| Formato Validado | `Formato_De_Inscripcion_Validado__c`| Checkbox | No* | *Requerido solo para la conversión posterior. |

## 6. Post-condiciones
1.  Un nuevo registro de **Lead** existe en la base de datos.
2.  El estado del lead es "Abierto" (o el estado por defecto configurado).
3.  El campo `IsConverted` es `False`.

---

<img width="21537" height="8264" alt="CU-VE-002" src="https://github.com/user-attachments/assets/e5b8617f-f8d0-4633-80cb-8a5939e4e3fc" />
