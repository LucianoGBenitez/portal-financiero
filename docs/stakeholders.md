# Stakeholders

### Personal Administrativo Contable

**Tipo:** Interno  
**Por qué es clave:** _Es el responsable principal de la carga mensual de planillas de pago y de la sincronización de datos maestros de CBU._

---

### Analista Contable

**Tipo:** Interno  
**Por qué es clave:** _Responsable de realizar la sincronización diaria de extractos bancarios. Su meta es validar que la información se refleje correctamente en la base de datos de SAP correspondiente por empresa y banco._

---

### Jefatura de Administración

**Tipo:** Interno   
**Por qué es clave:** _Es la responsable de la supervisión, el control financiero y la recepción de comprobantes de control por WhatsApp._

---

### Administrador de Infraestructura / Analista IT

**Tipo:** Interno  
**Por qué es clave:** _Se encarga de la infraestructura y monitoreo del correcto funcionamiento del servidor web, mantiene conexiones del Service Layer de SAP, resguarda las credenciales en el archivo de entorno y audita las ejecuciones asincrónicas del sistema._

---

### Interbanking API Gateway

**Tipo:** Sistema externo  
**Por qué es clave:** _Sistema encargado de consulta de movimientos, transferencia electrónica y validación financiera._

---

### SAP Business One Service Layer

**Tipo:** Sistema externo  
**Por qué es clave:** _Sistema encargado de registrar movimientos contables, administrar socios de negocio y gestionar cuentas bancarias._

---

### Padrón bancario externo (Interbanking)

**Tipo:** Sistema externo  
**Por qué es clave:** _Fuente de datos externa que provee la nómina y registros bancarios actualizados para contrastar contra el maestro de socios de negocio de SAP y sincronizar CBU._

---

### Servicio de WhatsApp

**Tipo:** Sistema externo  
**Por qué es clave:** _Canal de mensajería automatizado encargado de transportar y despachar en tiempo real las notificaciones y los reportes en PDF de control a la Jefatura de Administración tras la generación de transferencias TEF y el impacto de extractos._

---

## Tabla resumen

| Stakeholder | Tipo | Nivel de impacto |
|-------------|------|-----------------|
| Personal Administrativo Contable | Interno | Alto |
| Analista Contable | Interno | Alto |
| Jefatura de Administración | Interno | Bajo |
| Administrador de Infraestructura / Analista IT | Interno | Medio |
| Interbanking API Gateway | Sistema externo | Alto |
| SAP Business One Service Layer | Sistema externo | Alto |
| Padrón bancario externo (Interbanking) | Sistema externo | Medio |
| Servicio de WhatsApp | Sistema externo | Bajo |

---

