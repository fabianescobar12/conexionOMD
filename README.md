# Configuración de Azure AD y Power BI para OpenMetadata

## Paso 1: Crear una aplicación en Azure AD y configurar Power BI

### Crear una aplicación en Azure AD:
1. Inicia sesión en [Azure Portal](https://portal.azure.com).
2. Ve a **Azure Active Directory (Azure AD)** y selecciona **Registros de aplicaciones**.
3. Haz clic en **Nuevo registro**:
   - **Nombre:** Escribe un nombre para tu aplicación, por ejemplo, "OpenMetadata PowerBI Connector".
   - **Tipo de cuenta admitida:** Selecciona "Cuentas en este directorio organizativo".
   - **Redirección URI:** Puedes dejarlo en blanco por ahora.
4. Haz clic en **Registrar**.

### Configurar la consola de administración de Power BI:
1. Inicia sesión en Power BI como administrador.
2. Ve a **Configuración del inquilino (Tenant Settings)**.
3. Habilita las siguientes opciones:
   - Permitir que los principios de servicio usen las APIs de Power BI.
   - Permitir que los principios de servicio usen las APIs de administrador de Power BI en modo solo lectura.
   - Mejorar las respuestas de las APIs de administración con metadatos detallados.

## Paso 2: Configurar permisos en Azure AD

1. En **Azure AD > Registros de aplicaciones**, selecciona la aplicación que creaste.
2. Ve a **Permisos de la API > Agregar un permiso**.
3. Selecciona **APIs de Microsoft > Power BI Service** y otorga los siguientes permisos:
   - **Dashboard.Read.All** (Obligatorio).
   - **Dataset.Read.All** (Opcional, pero recomendado para obtener datos de conjuntos y modelos).
4. Haz clic en **Conceder consentimiento de administrador** para aprobar los permisos.

## Paso 3: Generar un secreto de cliente

1. En la página de tu aplicación en Azure AD, ve a **Certificados y secretos**.
2. Haz clic en **Nuevo secreto de cliente**.
3. Proporciona una descripción y selecciona la duración (por ejemplo, 1 año o sin vencimiento).
4. Haz clic en **Agregar** y copia el valor del secreto generado. **Guárdalo en un lugar seguro**, ya que solo podrás verlo una vez.

## Paso 4: Obtener el Tenant ID, Client ID y Authority URI

1. **Tenant ID:**
   - En la página de tu aplicación, ve a **Información general**.
   - Copia el **Directory (tenant) ID**.
2. **Client ID:**
   - Copia el **Application (client) ID** de la misma página.
3. **Authority URI:**
   - Usa el valor por defecto: `https://login.microsoftonline.com/`.

## Paso 5: Crear un nuevo Workspace en Power BI

1. Inicia sesión en Power BI y crea un nuevo **workspace**.
2. Asigna el principio de servicio de tu aplicación como miembro o administrador del workspace.
   - Esto es necesario porque los principios de servicio solo funcionan con workspaces nuevos.

## Paso 6: Configurar la conexión en OpenMetadata

1. Inicia sesión en OpenMetadata y ve a **Settings > Services**.
2. Crea un nuevo servicio para Power BI y proporciona los siguientes detalles:
   - **Client ID:** El **Application (client) ID** que obtuviste en Azure AD.
   - **Client Secret:** El secreto que generaste.
   - **Tenant ID:** El **Directory (tenant) ID**.
   - **Authority URI:** Usa `https://login.microsoftonline.com/`.
   - **Host URL:** Usa `https://app.powerbi.com` si estás usando Power BI en la nube.
   - **Scopes:** Usa el valor `https://analysis.windows.net/powerbi/api/.default`.
   - Establece el límite de paginación si es necesario (por defecto es 100).
   - Habilita las APIs de administración si deseas que OpenMetadata recupere datos de todos los workspaces en Power BI.

## Paso 7: Verificar la conexión

1. Guarda la configuración y prueba la conexión en OpenMetadata.
2. Si la conexión es exitosa, OpenMetadata comenzará a importar dashboards, gráficos y conjuntos de datos desde Power BI.

## Notas Adicionales

- **Seguridad:** Asegúrate de que el secreto de cliente y los tokens sean manejados de forma segura.
- **Limitaciones:** Si no otorgas los permisos opcionales (`Dataset.Read.All`), OpenMetadata no podrá procesar la información de lineage ni los modelos de datos.
- **Documentación:** Consulta la [documentación oficial de Power BI](https://learn.microsoft.com/power-bi/developer) para más detalles sobre las APIs.

