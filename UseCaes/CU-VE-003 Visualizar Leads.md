# Especificación de Caso de Uso: Visualizar Leads

**ID:** CU-VE-003

**Actor(es):** Promotor de Ventas (Agente), Responsable de Ventas (Gerente/Admin)  

**Objeto Salesforce:** Lead (Prospecto)

---

## 1. Descripción
El usuario accede a la pestaña de **Prospectos (Leads)** en Salesforce para visualizar, filtrar y organizar los registros. La vista se adapta automáticamente según el rol: los Promotores se enfocan en sus propios leads ("Mis prospectos"), mientras que los Responsables pueden supervisar la base completa y aplicar filtros avanzados por programa académico o evento.

## 2. Precondiciones
1.  **Acceso:** El usuario ha iniciado sesión en Salesforce.
2.  **Permisos:** El usuario tiene permisos de lectura sobre el objeto `Lead`.
3.  **Datos:** Existen registros de prospectos en la base de datos.

## 3. Flujo Básico: Vista del Responsable de Ventas (Supervisión)

1.  **Navegación:** El Responsable hace clic en la pestaña **Prospectos** en la barra de navegación de la aplicación.
2.  **Selección de Vista de Lista:**
    * Por defecto, el sistema puede mostrar "Vistos recientemente".
    * El usuario hace clic en el selector de vistas (la flecha al lado del nombre de la lista) y selecciona **"Todos los prospectos abiertos"**.
3.  **Visualización:** El sistema despliega una tabla con todos los leads. Las columnas incluyen: *Nombre*, *Título*, *Compañía*, *Teléfono*, *Email*, *Estado* y *Propietario*
4.  **Vista Avanzada:**
    * El Responsable hace clic en **Vistos Recientemente** en la esquina superior izquierda.
    * Selecciona la vista que desea utilizar:
        * **Prospectos Asignado:** Muestra los leads que ya han sido asignados a un agente de ventas.
        * **Prospectos Sin Asignar:** Muestra los leads que NO han sido asignados a un agente de ventas.
        * **Prospectos de Hoy:** Muestra los leads que han sido creados hoy.
5.  **Interacción:** Al hacer clic en el *Nombre* de un prospecto, el sistema redirige al detalle del registro.

## 4. Flujos Alternativos

### A. Vista del Promotor de Ventas (Agente)
* **Contexto:** El Promotor necesita ver solo los leads que debe gestionar.
* **Acción:**
    1.  Ingresa a la pestaña **Prospectos**.
    2.  Selecciona la vista de lista **"Prospectos abiertos"** (All Open Leads).
* **Resultado:** Salesforce filtra automáticamente la base de datos mostrando solo los registros correspondientes.
* **Funcionalidad Adicional:** Puede usar la barra de búsqueda de la lista ("Buscar en esta lista...") para encontrar rápidamente un lead por nombre o teléfono.

### B. Búsqueda Global (Sin Resultados en Lista)
* **Contexto:** El usuario no encuentra al lead en la lista actual filtrada.
* **Acción:** Utiliza la barra de **Búsqueda Global** (parte superior de la pantalla) e ingresa el nombre, correo o teléfono.
* **Resultado:** Salesforce busca en toda la base de datos (independientemente de los filtros de la vista actual) y devuelve los resultados coincidentes.

## 5. Post-condiciones

1.  **Éxito:** El usuario visualiza la información requerida filtrada según sus necesidades. No se modifican datos (es una operación de lectura), a menos que se use la edición en línea (Inline Editing) disponible en las vistas de lista.
2.  **Persistencia de Vista:** Si el usuario "fija" una vista (pin), la próxima vez que entre verá esa lista por defecto.

---

<img width="21516" height="8223" alt="CU-VE-003" src="https://github.com/user-attachments/assets/eb023af1-1268-4466-ab5b-cb298bd0e158" />
