# Configuración de Azure AD y Power BI para OpenMetadata

## Paso 1: Crear una aplicación en Azure AD y configurar Power BI

### Crear una aplicación en Azure AD:
1. Iniciar sesión en [Azure Portal](https://portal.azure.com).
2. Ir a **Azure Active Directory (Azure AD)** y seleccionar **Registros de aplicaciones**.
3. Hacer clic en **Nuevo registro**:
   - **Nombre:** Escribir un nombre para la aplicación, por ejemplo, "OpenMetadata PowerBI Connector".
   - **Tipo de cuenta admitida:** Seleccionar "Cuentas en este directorio organizativo".
   - **Redirección URI:** Dejarlo en blanco por ahora.
4. Hacer clic en **Registrar**.

### Configurar la consola de administración de Power BI:
1. Iniciar sesión en Power BI como administrador.
2. Ir a **Configuración del inquilino (Tenant Settings)**.
3. Habilitar las siguientes opciones:
   - Permitir que las entidades de servicio puedan usar las API de Fabric.
   - Permitir que las entidades de servicio puedan acceder a las API de administración de solo lectura.
   - Permitir mejora de las respuestas de las API de administración con metadatos detallados.
   - Permitir mejora de las respuestas de las API de administración con expresiones DAX y mashup.
   - Permitir que los usuarios puedan editar modelos de datos en el servicio Power BI.

## Paso 2: Configurar permisos en Azure AD

1. En **Azure AD > Registros de aplicaciones**, seleccionar la aplicación que se creó.
2. Ir a **Permisos de la API > Agregar un permiso**.
3. Seleccionar **APIs de Microsoft > Power BI Service > Permisos delegados** y otorgar los siguientes permisos:
   - **Dashboard.Read.All** (Obligatorio).
   - **Dataset.Read.All** (Opcional, pero recomendado para obtener datos de conjuntos y modelos).
4. Hacer clic en **Conceder consentimiento de administrador** para aprobar los permisos.

## Paso 3: Generar un secreto de cliente

1. En la página de la aplicación en Azure AD, ir a **Certificados y secretos**.
2. Hacer clic en **Nuevo secreto de cliente**.
3. Proporcionar una descripción y seleccionar la duración (por ejemplo, 1 año o sin vencimiento).
4. Hacer clic en **Agregar** y copiar el valor del secreto generado. **Guardar en un lugar seguro**, ya que solo se podrá ver una vez.

## Paso 4: Obtener el Tenant ID, Client ID y Authority URI

1. **Tenant ID:**
   - En la página de la aplicación, ir a **Información general**.
   - Copiar el **Directory (tenant) ID**.
2. **Client ID:**
   - Copiar el **Application (client) ID** de la misma página.
3. **Authority URI:**
   - Usar el valor por defecto: `https://login.microsoftonline.com/`.

## Paso 5: Crear un nuevo Workspace en Power BI

1. Iniciar sesión en Power BI y crear un nuevo **workspace**.
2. Asignar el principio de servicio de la aplicación como miembro o administrador del workspace.
   - Esto es necesario porque los principios de servicio solo funcionan con workspaces nuevos.

## Paso 6: Configurar la conexión en OpenMetadata

1. Iniciar sesión en OpenMetadata e ir a **Settings > Services**.
2. Crear un nuevo servicio para Power BI y proporcionar los siguientes detalles:
   - **Client ID:** El **Application (client) ID** que se obtuvo en Azure AD.
   - **Client Secret:** El secreto que se generó.
   - **Tenant ID:** El **Directory (tenant) ID**.
   - **Authority URI:** Usar `https://login.microsoftonline.com/`.
   - **Host URL:** Usar `https://app.powerbi.com` si se está usando Power BI en la nube.
   - **Scopes:** Usar el valor `https://analysis.windows.net/powerbi/api/.default`.
   - Establecer el límite de paginación si es necesario (por defecto es 100).
   - Habilitar las APIs de administración si se desea que OpenMetadata recupere datos de todos los workspaces en Power BI.

## Paso 7: Verificar la conexión

1. Guardar la configuración y probar la conexión en OpenMetadata.
2. Si la conexión es exitosa, OpenMetadata comenzará a importar dashboards, gráficos y conjuntos de datos desde Power BI.

## Notas Adicionales

- **Seguridad:** Asegurar que el secreto de cliente y los tokens sean manejados de forma segura.
- **Limitaciones:** Si no se otorgan los permisos opcionales (`Dataset.Read.All`), OpenMetadata no podrá procesar la información de lineage ni los modelos de datos.
- **Documentación:** Consultar la [documentación oficial de Power BI](https://learn.microsoft.com/power-bi/developer) para más detalles sobre las APIs.

