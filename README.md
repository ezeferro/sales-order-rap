# Sales Orders — RAP (Master SAP BTP)

Implementación RESTful Application Programming Model del proyecto final
"Sales Orders" (2234-LO-1127-SAP). Ambiente destino: **SAP BTP ABAP
Environment (Steampunk)**.

## Convención de nombres usada

Usuario SAP (últimos 4 dígitos): **1982** · Nombre base: **SALESORDER** / **SALESORDERITEM**

| Objeto | Nombre |
|---|---|
| Paquete | `Z1982` |
| Tabla Header | `ZSALESORDER_1982` |
| Tabla Items | `ZSALESORDERITEM_1982` |
| Domain (unidad de medida) | `ZDO_MEINS_1982` |
| Data Element (unidad de medida) | `ZDE_MEINS_1982` |

Los siguientes objetos de las próximas fases seguirán el mismo patrón:
`ZI_SALESORDER_1982` / `ZI_SALESORDERITEM_1982` (CDS interface),
`ZC_SALESORDER_1982` / `ZC_SALESORDERITEM_1982` (CDS projection),
`ZCL_SALESORDER_1982` (clase de implementación de behavior),
`ZSD_SALESORDER_1982` (service definition), `ZSB_SALESORDER_1982` (service binding).

## Desvíos respecto al documento de requerimientos original

El documento tiene algunas inconsistencias de tipeo que se corrigieron:

- `CREATEDON` y `DELIVERYDATE`: el documento los mezcla con tipos `CHAR`/`UNIT`
  incorrectamente — se implementaron como `DATS` (fecha), que es lo que
  describe su propósito.
- `IMAGEURL`: el documento indica longitud 8, insuficiente para una URL — se
  amplió a `CHAR(255)`.
- **Tabla Items — clave compuesta**: el documento solo lista `ID` como clave
  (además de `CLIENT`), lo cual no permite más de un ítem por orden. Se
  agregó el campo `PARENT_ID` (referencia a `ZSALESORDER_1982-ID`) como
  parte de la clave, para soportar la composición Header → Items.
- `ORDERSTATUS` y el resto de los campos se mantuvieron **tal cual el spec**
  (`INT1`), sin domain propio.

## Estructura de este repositorio (formato abapGit)

```
.abapgit.xml
src/
  z1982.devc.xml                  Paquete
  zdo_meins_1982.doma.xml         Domain - unidad de medida
  zde_meins_1982.dtel.xml         Data element - unidad de medida
  zsalesorder_1982.tabl.xml       Tabla Header
  zsalesorderitem_1982.tabl.xml   Tabla Items
```

## Cómo importarlo en Eclipse (ADT)

1. Asegurate de tener el plugin **abapGit** instalado en ADT (o usá la app
   web de abapGit si tu sistema la tiene desplegada).
2. Creá el paquete `Z1982` manualmente si tu sistema no permite que abapGit
   cree paquetes directamente (algunos tenants de ABAP Environment lo
   restringen vía wizard).
3. Vinculá este repositorio Git al paquete y hacé **Pull**.
4. Activá los objetos en este orden: Domain → Data Element → Tablas.

> **Nota sobre fidelidad del XML:** los objetos de diccionario (domain, data
> element, tabla) no tienen "código fuente" — son metadatos serializados en
> un XML interno de SAP. Generé estos archivos siguiendo el esquema estándar
> de abapGit, pero si algún objeto da error de parsing al activarlo, decime
> el mensaje exacto y lo corrijo, o lo recreamos a mano en el wizard de ADT
> usando esta misma tabla de campos como referencia — te va a tomar 2
> minutos por objeto.

## Roadmap (próximas fases)

- [ ] Fase 2: CDS interface views + projection views + metadata extensions
- [ ] Fase 3: Behavior definitions (managed) + clase de implementación
- [ ] Fase 4: Service definition + Service binding (OData V4 UI)
- [ ] Fase 5: App Fiori Elements (List Report + Object Page)
