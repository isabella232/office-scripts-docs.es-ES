---
title: Grabar, editar y crear scripts de Office en Excel en la Web
description: Un tutorial sobre los conceptos básicos de scripts de Office que incluye la grabación de scripts en la Grabadora de acciones y la escritura de datos en un libro.
ms.date: 01/27/2020
localization_priority: Priority
ms.openlocfilehash: 1971ff2ffd80554beb6ac561677ee3384f87ca81
ms.sourcegitcommit: b075eed5a6f275274fbbf6d62633219eac416f26
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "42700313"
---
# <a name="record-edit-and-create-office-scripts-in-excel-on-the-web"></a>Grabar, editar y crear scripts de Office en Excel en la Web

Este tutorial le enseñará los conceptos básicos de la grabación, la edición y la escritura de un script de Office para Excel en la Web.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [Preview note](../includes/preview-note.md)]

Antes de iniciar este tutorial, necesitará acceder a los scripts de Office. Esto requiere lo siguiente:

- [Excel en la Web](https://www.office.com/launch/excel)
- Pida a su administrador que [habilite los scripts de Office para su organización](https://support.office.com/article/office-scripts-settings-in-m365-19d3c51a-6ca2-40ab-978d-60fa49554dcf), lo que agrega la barra de herramientas **Automatizar** a la cinta de opciones.

> [!IMPORTANT]
> Este tutorial está diseñado para las personas con conocimientos a nivel intermedio de JavaScript o TypeScript. Si no está familiarizado con JavaScript, le recomendamos que revise el [Tutorial de JavaScript de Mozilla](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Introduction). Para obtener más información sobre el entorno de los scripts, visite [Scripts de Office en Excel en la Web](../overview/excel.md).

## <a name="add-data-and-record-a-basic-script"></a>Agregar datos y grabar un script básico

En primer lugar, necesitaremos algunos datos y un pequeño script inicial.

1. Cree un libro nuevo en Excel para la Web.
2. Copie los siguientes datos de ventas de frutas y péguelos en la hoja de cálculo, comenzando por la celda **A1**.

    |Fruta |2018 |2019 |
    |:---|:---|:---|
    |Naranjas |1000 |1200 |
    |Limones |800 |900 |
    |Limas |600 |500 |
    |Pomelos |900 |700 |

3. Abra la pestaña **Automatizar**. Si no ve la pestaña **Automatizar**, presione la flecha desplegable para comprobar el desbordamiento de la cinta de opciones.
4. Haga clic en el botón **Guardar acciones**.
5. Seleccione las celdas **A2:C2** (la fila "Naranjas") y configure el color de relleno como naranja.
6. Detenga la grabación pulsando el botón **Detener**.
7. Rellene el campo **Nombre del script** con un nombre que luego vaya a recordar.
8. *Opcional:* rellenar el campo **Descripción** con una descripción relevante. Esto ofrece un contexto sobre lo que hace el script. Para el tutorial, puede usar "Distinguiendo las filas de una tabla con colores".

   > [!TIP]
   > Puede editar una descripción del script más adelante desde el panel de **Detalles del script**, que se encuentra en el menú **...** del Editor de código.

9. Para guardar el script, presione el botón **Guardar**.

    La hoja de cálculo debe tener este aspecto (no se preocupe si el color es diferente):

    ![Una fila de datos de ventas de frutas con la fila "Naranjas" resaltada en naranja.](../images/tutorial-1.png)

## <a name="edit-an-existing-script"></a>Editar un script existente

El script anterior pinta la fila "Naranja" de color naranja. Ahora, agreguemos una fila amarilla a "Limones".

1. Abra la pestaña **Automatizar**.
2. Presione el botón del **Editor de código**.
3. Abra el script que anotó en la sección anterior. Debería ver algo parecido a esto en el código:

    ```TypeScript
    async function main(context: Excel.RequestContext) {
      // Set fill color to FFC000 for range Sheet1!A2:C2
      let workbook = context.workbook;
      let worksheets = workbook.worksheets;
      let selectedSheet = worksheets.getActiveWorksheet();
      selectedSheet.getRange("A2:C2").format.fill.color = "FFC000";
    }
    ```

    Este código accede a la colección de hojas de cálculo del libro para obtener la hoja de cálculo actual. Después, establece el color de relleno del rango **A2:C2**.

    Los rangos son una parte fundamental de las secuencias de comandos de Office en Excel en la Web. Un rango es un bloque de celdas contiguo y rectangular que contiene valores, fórmulas y formatos. Constituyen la estructura básica de las celdas y se usan para realizar la mayoría de las tareas de scripts.

4. Agregue la línea siguiente al final del script (entre el lugar en el que se establece el  y aparece el `}` de cierre):

    ```TypeScript
    selectedSheet.getRange("A3:C3").format.fill.color = "yellow";
    ```

5. Para probar el script, presione **Ejecutar**. El libro tendrá ahora el siguiente aspecto:

    ![Una fila de datos de ventas con la fila "Naranjas" resaltada en naranja y la fila "Limones" ahora resaltada en amarillo.](../images/tutorial-2.png)

## <a name="create-a-table"></a>Crear una tabla

Vamos a convertir estos datos de ventas de frutas en una tabla. Usaremos nuestro script para todo este proceso.

1. Agregue la línea siguiente al final del script (antes del `}` de cierre):

    ```TypeScript
    let table = selectedSheet.tables.add("A1:C5", true);
    ```

2. Esa llamada devuelve un objeto de `Table`. Vamos a usar la tabla para ordenar los datos. Ordenaremos los datos de menor a mayor en función de los valores de la columna "Frutas". Agregue la siguiente línea después de la creación de tabla:

    ```TypeScript
    table.sort.apply([{ key: 0, ascending: true }]);
    ```

    Su script debe tener este aspecto:

    ```TypeScript
    async function main(context: Excel.RequestContext) {
      // Set fill color to FFC000 for range Sheet1!A2:C2
      let workbook = context.workbook;
      let worksheets = workbook.worksheets;
      let selectedSheet = worksheets.getActiveWorksheet();
      selectedSheet.getRange("A2:C2").format.fill.color = "FFC000";
      selectedSheet.getRange("A3:C3").format.fill.color = "yellow";
      let table = selectedSheet.tables.add("A1:C5", true);
      table.sort.apply([{ key: 0, ascending: true }]);
    }
    ```

    Las tablas tienen un objeto `TableSort` al que se accede mediante la propiedad `Table.sort`. Puede aplicar un criterio de ordenación a ese objeto. El método `apply` acepta una matriz de objetos `SortField`. En este caso, solo tenemos un criterio de ordenación, por lo que solo usamos un `SortField`. `key: 0` establece los valores que definen la ordenación de la columna como "0" (que es la primera columna de la tabla **A** en este caso). `ascending: true` ordena los datos de menor a mayor (en lugar de mayor a menor).

3. Ejecute el script. Debería ver una tabla como esta:

    ![Una tabla de ventas de frutas ordenada.](../images/tutorial-3.png)

    > [!NOTE]
    > Si vuelve a ejecutar el script, se producirá un error. Esto se debe a que no se puede crear una tabla encima de otra. Sin embargo, puede ejecutar el script en otra hoja de cálculo o en un libro.

### <a name="re-run-the-script"></a>Ejecute el script de nuevo

1. Crear una nueva hoja de cálculo en el libro actual.
2. Copie los datos de frutas del principio del tutorial y péguelos en la nueva hoja de cálculo, comenzando en la celda **A1**.
3. Ejecute el script.

## <a name="next-steps"></a>Pasos siguientes

Complete el tutorial [Leer datos de libros con scripts de Office en Excel en la Web](excel-read-tutorial.md). En él aprenderá a leer datos de un libro con un script de Office.