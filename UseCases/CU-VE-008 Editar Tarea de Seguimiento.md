# Especificación de Caso de Uso: Editar Tarea de Seguimiento

**ID:** CU-VE-009  
**Actor(es):** Promotor de Ventas (Agente)  
**Objeto Salesforce:** Task (Tarea)

---

## 1. Descripción
El Promotor de Ventas modifica los detalles de una tarea previamente programada. Esto es común cuando cambian las prioridades, el cliente solicita reagendar una llamada o se necesita agregar información adicional (contexto) a la tarea antes de completarla.

## 2. Precondiciones
1.  **Existencia:** Existe una tarea pendiente (Estado "Not Started" o "In Progress") en el sistema.
2.  **Permisos:** El usuario es el propietario de la tarea o tiene permisos de edición sobre las actividades del equipo.
3.  **Acceso:** El usuario se encuentra en la ficha de la Oportunidad/Lead, en la pestaña de Tareas o en la Página de Inicio.

## 3. Flujo Básico (Edición desde Cronología de Actividad)

1.  **Navegación:** El Promotor accede al registro (Oportunidad o Lead) donde se encuentra la tarea.
2.  **Identificación:** En el componente **Cronología de Actividades** (*Activity Timeline*), localiza la tarea en la sección "Próximos pasos" (*Next Steps*).
3.  **Menú de Acción:** Hace clic en la flecha desplegable situada a la derecha de la tarea específica.
4.  **Selección:** Elige la opción **"Editar"** (*Edit*).
5.  **Modificación:** Se abre una ventana modal con los datos actuales. El Promotor actualiza los campos necesarios:
    * **Cambio de Fecha:** Modifica la *Fecha de Vencimiento* porque el cliente pospuso la cita.
    * **Cambio de Prioridad:** Cambia de "Normal" a "Alta".
    * **Actualización de Comentarios:** Agrega notas nuevas en el campo de descripción.
6.  **Guardar:** Hace clic en el botón **"Guardar"**.
7.  **Confirmación:**
    * El sistema muestra un mensaje de éxito: *"Tarea [Asunto] guardada"*.
    * La tarea se reposiciona en la cronología si la fecha fue modificada.

## 4. Flujos Alternativos

### A. Reprogramación Rápida (Drag & Drop en Calendario)
* **Contexto:** El usuario prefiere una vista visual de su agenda.
* **Acción:**
    1.  Navega a la pestaña **Calendario** en Salesforce.
    2.  Identifica el bloque de la tarea.
    3.  Arrastra y suelta (*Drag and Drop*) la tarea a un nuevo día/hora.
* **Resultado:** La fecha de vencimiento (`ActivityDate`) se actualiza automáticamente.

### B. Edición en Línea (List Views)
* **Contexto:** El usuario está revisando su lista de "Tareas de hoy" en formato tabla.
* **Acción:**
    1.  Pasa el mouse sobre el campo que quiere cambiar (ej. Fecha o Estado).
    2.  Hace clic en el icono de **Lápiz** (Edición en línea).
    3.  Cambia el valor y hace clic en "Guardar" en la parte inferior de la lista.
* **Resultado:** Se actualiza el registro sin necesidad de abrir la ventana modal completa.

### C. Marcar como Completada (Edición de Estado)
* *Nota: A veces se considera un caso de uso separado, pero técnicamente es una edición.*
* **Acción:** El usuario marca la casilla de verificación (Check) al lado de la tarea en la cronología.
* **Resultado:** El estado cambia a "Completed" y la tarea se mueve de "Próximos pasos" a "Actividades pasadas".

## 5. Excepciones

### A. Tarea de otro Propietario (Error de Privilegios)
* **Disparador:** Un agente intenta editar una tarea asignada a un compañero (y la configuración de seguridad es privada).
* **Resultado:** El sistema no muestra la opción "Editar" o muestra un mensaje de error: *"No tiene los permisos necesarios para editar este registro"*.

### B. Tarea Archivada
* **Disparador:** Intentar editar una tarea muy antigua (que Salesforce ha archivado por antigüedad, generalmente > 365 días).
* **Resultado:** El registro puede ser de "Solo Lectura".

## 6. Datos Modificables (Diccionario)

| Campo | API Name | Tipo de Cambio Común |
| :--- | :--- | :--- |
| Fecha de Vencimiento | `ActivityDate` | Reagendar (Posponer/Adelantar). |
| Estado | `Status` | Cambiar progreso (ej. "En espera"). |
| Prioridad | `Priority` | Escalar urgencia. |
| Descripción | `Description` | Agregar contexto o notas preparatorias. |
| Asignado a | `OwnerId` | Delegar la tarea a otro usuario. |

## 7. Post-condiciones

1.  **Actualización:** La base de datos refleja los nuevos valores.
2.  **Recordatorios:** Si se cambió la fecha, el recordatorio (alerta) se ajusta a la nueva fecha.
3.  **Auditoría:** La fecha de "Última modificación" (*LastModifiedDate*) del registro se actualiza.

---

<img width="10056" height="4048" alt="CU-VE-008" src="https://github.com/user-attachments/assets/55364f3c-dc34-4b7c-8ed6-6cbc92fac966" />
