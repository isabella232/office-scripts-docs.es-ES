---
title: Scripts de ejemplo para scripts de Office en Excel en la web
description: Una colección de ejemplos de código para usar con scripts de Office en Excel en la Web.
ms.date: 02/19/2020
localization_priority: Normal
ms.openlocfilehash: abb4064dfde8b644035e725832e481e6463e979e
ms.sourcegitcommit: b075eed5a6f275274fbbf6d62633219eac416f26
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "42700416"
---
# <a name="sample-scripts-for-office-scripts-in-excel-on-the-web-preview"></a>Scripts de ejemplo para scripts de Office en Excel en la web (vista previa)

Los siguientes ejemplos son scripts sencillos que puede probar en sus propios libros. Para usarlas en Excel en la web:

1. Abra la ficha **automatizar** .
2. Presione el **Editor de código**.
3. Presione **nueva secuencia de comandos** en el panel de tareas del editor de código.
4. Reemplace todo el script por el ejemplo de su elección.
5. Presione **Ejecutar** en el panel de tareas del editor de código.

[!INCLUDE [Preview note](../includes/preview-note.md)]

## <a name="scripting-basics"></a>Conceptos básicos de scripting

Estos ejemplos muestran bloques de creación fundamentales para los scripts de Office. Agréguelos a los scripts para ampliar la solución y resolver problemas comunes.

### <a name="read-and-log-one-cell"></a>Leer e iniciar sesión en una celda

En este ejemplo se lee el valor de **a1** y se imprime en la consola.

``` TypeScript
async function main(context: Excel.RequestContext) {
  // Get the current worksheet.
  let selectedSheet = context.workbook.worksheets.getActiveWorksheet();

  // Get the value of cell A1.
  let range = selectedSheet.getRange("A1");
  range.load("values");
  await context.sync();

  // Print the value of A1.
  console.log(range.values);
}
```

### <a name="work-with-dates"></a>Trabajar con fechas

En este ejemplo se utiliza el objeto [Date](https://developer.mozilla.org/docs/web/javascript/reference/global_objects/date) de JavaScript para obtener la fecha y hora actuales y, a continuación, se escriben los valores en dos celdas de la hoja de cálculo activa.

```TypeScript
async function main(context: Excel.RequestContext) {
  // Get the cells at A1 and B1.
  let dateRange = context.workbook.worksheets.getActiveWorksheet().getRange("A1");
  let timeRange = context.workbook.worksheets.getActiveWorksheet().getRange("B1");

  // Get the current date and time with the JavaScript Date object.
  let date = new Date(Date.now());

  // Add the date string to A1.
  dateRange.values = [[date.toLocaleDateString()]];
  
  // Add the time string to B1.
  timeRange.values = [[date.toLocaleTimeString()]];
}
```

## <a name="display-data"></a>Mostrar datos

En estos ejemplos se muestra cómo trabajar con los datos de la hoja de cálculo y proporcionar a los usuarios una vista o organización mejor.

### <a name="apply-conditional-formatting"></a>Aplicar formato condicional

En este ejemplo se aplica formato condicional al intervalo que se usa actualmente en la hoja de cálculo. El formato condicional es un relleno verde para el 10% de los valores principales.

```TypeScript
async function main(context: Excel.RequestContext) {
  // Get the current worksheet.
  let selectedSheet = context.workbook.worksheets.getActiveWorksheet();

  // Get the used range in the worksheet.
  let range = selectedSheet.getUsedRange();

  // Set the fill color to green for the top 10% of values in the range.
  let conditionalFormat = range.conditionalFormats.add(Excel.ConditionalFormatType.topBottom);
  conditionalFormat.topBottom.format.fill.color = "green";
  conditionalFormat.topBottom.rule = {
    rank: 10, // The percentage threshold.
    type: Excel.ConditionalTopBottomCriterionType.topPercent // The type of the top/bottom condition.
  };
}
```

### <a name="create-a-sorted-table"></a>Crear una tabla ordenada

En este ejemplo se crea una tabla a partir del rango usado de la hoja de cálculo actual y, a continuación, se ordena basándose en la primera columna.

```TypeScript
async function main(context: Excel.RequestContext) {
  // Get the current worksheet.
  let selectedSheet = context.workbook.worksheets.getActiveWorksheet();

  // Create a table with the used cells.
  let usedRange = selectedSheet.getUsedRange();
  let newTable = selectedSheet.tables.add(usedRange, true);

  // Sort the table using the first column.
  newTable.sort.apply([{ key: 0, ascending: true }]);
}
```

## <a name="collaboration"></a>Colaboración

En estos ejemplos se muestra cómo trabajar con las características relacionadas con la colaboración de Excel, como los comentarios.

### <a name="delete-resolved-comments"></a>Eliminar comentarios resueltos

Este ejemplo elimina todos los comentarios resueltos de la hoja de cálculo actual.

```TypeScript
async function main(context: Excel.RequestContext) {
  // Get the current worksheet.
  let selectedSheet = context.workbook.worksheets.getActiveWorksheet();

  // Get the comments on this worksheet.
  let comments = selectedSheet.comments;
  comments.load("items/resolved");
  await context.sync();

  // Delete the resolved comments.
  comments.items.forEach((comment) => {
      if (comment.resolved) {
          comment.delete();
      }
  });
}
```

## <a name="scenario-samples"></a>Ejemplos de escenario

Para obtener ejemplos que muestren soluciones de gran tamaño para el mundo real, visite ejemplos [de escenarios de Office scripts](scenarios/sample-scenario-overview.md).

## <a name="suggest-new-samples"></a>Sugerir nuevos ejemplos

Agradecemos las sugerencias para los nuevos ejemplos. Si hay un escenario común que ayudaría a otros programadores de scripts, indíquenos en la sección Comentarios a continuación.