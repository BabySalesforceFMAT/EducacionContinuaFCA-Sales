# Especificación de Caso de Uso: Asignar Leads

**ID:** CU-VE-004    
**Actor(es):** Responsable de Ventas (Gerente/Jefe de Área)  
**Objeto Salesforce:** Lead (Prospecto)

---

## 1. Descripción
El Responsable de Ventas distribuye los prospectos entrantes entre los Promotores de Ventas (Agentes). En términos de Salesforce, esto implica cambiar el campo **Propietario del registro (`OwnerId`)**. Esta acción puede realizarse de forma individual o masiva desde las Vistas de Lista para optimizar el flujo de trabajo, asegurando que cada lead sea atendido por un agente específico.

## 2. Precondiciones
1.  **Permisos:** El usuario debe tener el permiso de perfil "Transferir prospectos" (*Transfer Leads*) o ser jerárquicamente superior al propietario actual del registro.
2.  **Estado Inicial:** Existen leads en la base de datos que pertenecen a una "Cola" (*Queue*) de asignación o a un usuario genérico, pendientes de distribución.
3.  **Usuarios Activos:** Los Promotores de Ventas deben tener usuarios activos en Salesforce para poder ser seleccionados como propietarios.

## 3. Flujo Básico (Asignación Manual Masiva desde Lista)

1.  **Navegación:** El Responsable accede a la pestaña **Prospectos (Leads)**.
2.  **Filtrado:** Selecciona una Vista de Lista adecuada, por ejemplo: **"Prospectos sin asignar"**.
    * *Nota:* El Responsable puede verificar columnas clave como `Programa_de_Interes__c` o `EventoEC__c` para decidir qué agente es el más apto para atender el lead.
3.  **Selección:** Marca las casillas de verificación situadas a la izquierda de los registros que desea asignar (puede seleccionar uno o varios).
4.  **Acción:** Hace clic en el botón **"Cambiar propietario"** (*Change Owner*) situado en la barra de acciones superior de la lista.
5.  **Configuración de Asignación:**
    * Se abre una ventana emergente.
    * Busca y selecciona el nombre del **Promotor de Ventas** (Agente) deseado.
    * (Opcional) Marca la casilla *"Enviar notificación por correo electrónico"* para alertar al agente.
6.  **Confirmación:** Hace clic en **"Enviar"**.
7.  **Procesamiento del Sistema:**
    * Salesforce actualiza el campo `OwnerId` de todos los registros seleccionados.
    * Si se marcó la casilla, envía un email automático al nuevo propietario.
8.  **Resultado:** Los leads seleccionados ahora son asignados al nuevo agente de ventas.

## 4. Flujos Alternativos

### B. Uso de Reglas de Asignación
* **Contexto:** Se desea aplicar lógica automática sin selección manual.
* **Acción:** Al crear o editar un lead, el usuario marca la casilla **"Asignar usando reglas de asignación activas"**.
* **Resultado:** Salesforce evalúa los criterios (ej. Si `Programa_de_Interes__c` = "Finanzas", asignar a Agente X) y cambia el propietario automáticamente.

### C. Error de Permisos
* **Disparador:** Un Agente intenta asignarse leads de otro compañero sin tener permisos de transferencia.
* **Resultado:** El sistema oculta el botón "Cambiar propietario" o muestra un mensaje de error: *"Privilegios insuficientes"*.

## 5. Datos Afectados y Lógica de Negocio

| Campo | API Name | Acción del Sistema | Notas Importantes |
| :--- | :--- | :--- | :--- |
| Propietario | `OwnerId` | **Actualización** | Cambia del ID de la Cola/Gerente al ID del Agente. |
| Estado | `Status` | *Sin Cambio Automático* | Salesforce estándar no cambia el estado al asignar. Si se requiere que pase de "Nuevo" a "Asignado", se debe configurar un *Flow*. |
| Programa de Interés | `Programa_de_Interes__c` | Lectura | Criterio de decisión para asignar al experto adecuado. |
| Evento de Interés | `EventoEC__c` | Lectura | Criterio de decisión. |

## 6. Post-condiciones

1.  **Visibilidad:** El Agente asignado ahora tiene acceso de lectura/escritura sobre los leads (según el modelo de seguridad privada).
2.  **Vistas de Lista:** Los registros aparecen en la vista **"Mis prospectos abiertos"** del Agente.
3.  **Auditoría:** Se genera una entrada en el historial del registro indicando: *"Propietario cambiado de [Cola] a [Nombre Agente]"*.

---

<img width="9252" height="4048" alt="CU-VE-004" src="https://github.com/user-attachments/assets/61885c8c-a1f0-4829-b066-db36a1f19573" />
