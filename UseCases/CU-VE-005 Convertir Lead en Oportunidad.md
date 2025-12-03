# Especificación de Caso de Uso: Convertir Lead en Oportunidad

**ID:** CU-VE-005    
**Actor(es):** Promotor de Ventas (Agente)  
**Objetos Salesforce:** Lead (Origen) → Account, Contact, Opportunity (Destino)

---

## 1. Descripción
Cuando un Promotor de Ventas determina que un prospecto está calificado (tiene interés genuino, ha entregado documentación inicial o validado su inscripción), procede a **Convertir** el registro.

En Salesforce, esta acción es transaccional y transforma el **Lead** en tres nuevos registros (o los vincula si ya existen):
1.  **Cuenta (Account):** La entidad legal o familiar.
3.  **Oportunidad (Opportunity):** La venta potencial (Inscripción al programa).

## 2. Precondiciones
1.  **Estado:** El Lead existe y no ha sido convertido previamente (`IsConverted = False`).
2.  **Propiedad:** El usuario es propietario del Lead o tiene permisos para editarlo.
3.  **Regla de Negocio Crítica:** El campo **`Formato_De_Inscripcion_Validado__c`** debe estar marcado como `TRUE` (Verificado) antes de intentar la conversión.

## 3. Flujo Básico (Camino Feliz)

1.  **Acceso:** El Promotor accede a la ficha del Lead que desea convertir.
2.  **Verificación Previa (Paso Obligatorio):**
    * El Promotor revisa que los datos clave estén completos (Programa de Interés, Datos de contacto).
    * Edita el registro y marca la casilla **"Formato De Inscripción Validado"** (`Formato_De_Inscripcion_Validado__c`).
    * Guarda el cambio.
3.  **Iniciar Conversión:** Hace clic en el botón **"Convertir"** (*Convert*) ubicado en la barra de acciones o en la ruta de ventas.
4.  **Configuración de la Conversión:** El sistema despliega una ventana modal donde el Promotor confirma cómo se crearán los registros:
    * **Cuenta:** "Crear Nueva" (o adjuntar a una existente si detecta duplicados).
    * **Oportunidad:** "Crear Nueva". El nombre por defecto suele ser "[Nombre]". El Promotor puede editar este nombre.
    * **Estado del Lead Convertido:** Selecciona "Convertido".
5.  **Ejecución:** El Promotor hace clic en el botón **"Convertir"**.
6.  **Procesamiento:**
    * Salesforce transfiere los campos mapeados del Lead a la nueva Oportunidad (ej. `Programa_de_Interes__c` pasa a `Opportunity.Programa_de_Interes__c`).
    * El Lead original se marca como convertido y pasa a modo "solo lectura".
7.  **Finalización:** El sistema muestra un mensaje de éxito ("¡Se ha convertido su prospecto!") y redirige al usuario a la ficha de la nueva **Oportunidad** (o al Contacto/Cuenta según configuración).

## 4. Flujos Alternativos y Excepciones

### A. Bloqueo por Validación (Regla de Negocio)
* **Contexto:** El Promotor intenta convertir el Lead *sin* haber marcado la casilla de validación del formato.
* **Disparador:** Al hacer clic en el botón final "Convertir".
* **Resultado:** El sistema detiene el proceso y muestra el mensaje de error:
    > *"No puedes convertir este prospecto. El 'Formato de Inscripción' aún no ha sido marcado como validado."*
* **Solución:** El usuario debe cancelar, editar el Lead para marcar la casilla y volver a iniciar la conversión.

### B. Detección de Duplicados (Cuentas/Contactos Existentes)
* **Contexto:** El Lead pertenece a una empresa o persona que ya es cliente (ya existe en Salesforce).
* **Acción:** En la ventana de conversión (Paso 4), Salesforce muestra una alerta: *"Se encontraron duplicados coincidentes"*.
* **Resolución:** El Promotor selecciona **"Elegir existente"** en lugar de "Crear nuevo" para la Cuenta o el Contacto, vinculando así la nueva Oportunidad al cliente histórico.

### C. Conversión sin Oportunidad
* **Contexto:** El usuario solo quiere registrar al cliente en la base de datos pero no hay una venta inmediata activa.
* **Acción:** En la ventana de conversión, marca la casilla **"No crear una oportunidad al convertir"**.
* **Resultado:** Solo se crean la Cuenta.

## 5. Mapeo de Datos (Mapping)

Es crucial que el equipo de administración tenga configurado el mapeo de campos estándar y personalizados para no perder información durante la conversión:

| Campo Origen (Lead) | Campo Destino (Opportunity/Contact) | Notas |
| :--- | :--- | :--- |
| `Programa_de_Interes__c` | `Opportunity.Programa_de_Interes__c` | Clave para el seguimiento de venta. |
| `EventoEC__c` | `Opportunity.EventoEC__c` | Si aplica a un evento específico. |
| `Medio_de_Contacto__c` | `Contact.Medio_de_Contacto__c` | (Opcional) Para historial del cliente. |
| `Name` | `Contact.Name` | Se convierte en el nombre de la Cuenta. |

## 6. Post-condiciones

1.  **Lead:** El registro permanece en la base de datos pero su atributo `IsConverted` es `True`. Ya no aparece en las listas de "Prospectos Abiertos".
2.  **Oportunidad:** Se crea una nueva Oportunidad en etapa inicial (ej. "Prospecting" o "Qualification").
3.  **Relaciones:** La Oportunidad queda automáticamente relacionada a la Cuenta y al Contacto creados/seleccionados.

---

<img width="9252" height="4048" alt="CU-VE-005" src="https://github.com/user-attachments/assets/537acbdb-3638-43a5-8b29-f743dc8d707f" />
