# Table2Excel.js

This is a library to export html tables to excel sheets.

## Precondition

It has to be a row * column table

## Features

1. able to export with width, alignment and colors
2. extendable

## Dependencies

[ExcelJS](https://github.com/guyonroche/exceljs)

[FileSaver.js](https://github.com/eligrey/FileSaver.js)

## Live Demo

[Demo](https://jackgit.github.io/table2excel.js/index.html)

## Basic Usage

npm

```bash
npm i table2excel.js
```

table2excel.min.js (with ExcelJS packed)

```html
<script src="path/to/table2excel.min.js"></script>
```

table2excel.core.js (without ExcelJS packed)

```html
<script src="path/to/exceljs.min.js"></script>
<script src="path/to/table2excel.core.js"></script>
```

```js
const table2Excel = new Table2Excel(selector, options)  // new Table2Excel('table')
table2Excel.export(fileName, extension) // table2Excel.export('my-exported-table', 'xlsx')
```

`extension` can be `'xls'` or `'xlsx'`, default as `'xlsx'`

### selector

It's optional, and defaulted as `'table'`

### options

It's optional, and defaulted as:

```js
{
  workbook: {
    views: [{
      x: 0, y: 0, width: 10000, height: 20000,
      firstSheet: 0, activeTab: 1, visibility: 'visible'
    }]
  },
  widthRatio: .14,
  enableDefaultPlugins: true,
  plugins: []
}
```

`workbook` is options used while creating a workbook, please refer [exceljs#create-a-workbook](https://github.com/guyonroche/exceljs#create-a-workbook) for details.

`widthRatio` is a ratio that will be used while converting `width` style of html table cells to width of sheet cells.

## Plugins

Plugin helps to extend the ability of transforming table to excel.

Build-in plugins can be access like:

```js
Table2Excel.plugins.font
Table2Excel.plugins.alignment
Table2Excel.plugins.autoWidth
Table2Excel.plugins.form
Table2Excel.plugins.hyperlink
```

A plugin can be defined to join different phase of table to excel process, and in different phase, plugin is able to access different objects from context.

```js
{
  /**
   * after an empty workbook created
   * @param  {ExcelJS.Workbook} context.workbook
   * @param  {NodeList} context.tables   
   */
  workbookCreated ({ workbook, tables }) {},
  /**
   * after an empty worksheet created
   * @param  {ExcelJS.Workbook} workbook
   * @param  {NodeList} tables
   * @param  {ExcelJS.Worksheet} worksheet
   * @param  {HTMLTableElement} table
   */
  worksheetCreated ({ workbook, tables, worksheet, table }) {},
  /**
   * after a worksheet been filled with data from table
   * @param  {ExcelJS.Workbook} workbook
   * @param  {NodeList} tables
   * @param  {ExcelJS.Worksheet} worksheet
   * @param  {HTMLTableElement} table
   */
  worksheetCompleted ({ workbook, tables, worksheet, table }) {},
  /**
   * after an cell of worksheet created
   * @param  {ExcelJS.Workbook} workbook
   * @param  {NodeList} tables
   * @param  {ExcelJS.Worksheet} worksheet
   * @param  {HTMLTableElement} table
   * @param  {ExcelJS.Cell} workcell
   * @param  {HTMLTableCellElement} cell
   * @param  {colRange} [from, to]
   * @param  {rowRange} [from, to]
   */
  workcellCreated ({ workbook, tables, worksheet, table, workcell, cell, cellStyle, colRange, rowRange }) {}
}
```


Example 1, you can define a plugin to make some rows or columns hidden of exported excel:

```js
const table2Excel = new Table2Excel('table', {
  plugins: [{
    worksheetCompleted ({ workbook, tables, worksheet, table }) {
      worksheet.getRow(1).hidden = true
      worksheet.getColumn(1).hidden = true
    }
  }]
})
```

Example 2, you can add your customized cell parser for your table:

```js
const table2Excel = new Table2Excel('table', {
  plugins: [{
    workcellCreated ({ workbook, tables, worksheet, table, workcell, cell }) {
      workcell.value = { text: '', link: '' }
      workcell.style = {
        ...workcell.style,
        font: {},
        color: {}
      }
    }
  }]
})
```
