# Show & hide DataGrid columns programatically

A module that allows for showing and hiding DataGrid columns in scripts

# Version 
1.0

## Application Setup
1. Check the *Enable Style Sheet* checkbox in the application properties

## Database, Connector and DataGrid
1. Use the instructions from [this repo](https://github.com/stadium-software/samples-database) to setup the database and DataGrid for this sample

## Global Script Setup
1. Create a Global Script called "ColumnHiding"
2. Add two input parameters to the Global Script
   1. Action
   2. ColumnNumbers
   3. DataGridClass
3. Drag a *JavaScript* action into the script
4. Add the Javascript below into the JavaScript code property
```javascript
/* Stadium Script v1 - see https://github.com/stadium-software/datagrid-showhide-columns-programatically */
let arrCols = ~.Parameters.Input.ColumnNumbers;
let dgClassName = "." + ~.Parameters.Input.DataGridClass;
let action = ~.Parameters.Input.Action;
let hide = true;
if (action && action.toLowerCase() === "show") { 
    hide = false;
}
let table = document.querySelector(dgClassName + " table");
let head = document.head;
let style = document.createElement("style");
style.innerHTML = `.data-grid-container .table > tbody > tr > td.hidden-column, 
.data-grid-container .table > thead > tr > th.hidden-column {
position: absolute;
width: 1px;
height: 1px;
margin: -1px;
border: 0;
padding: 0;
white-space: nowrap;
clip-path: inset(100%);
clip: rect(0 0 0 0);
overflow: hidden;}`;
head.appendChild(style);
function initGrid() {
    for (let j = 0; j < arrCols.length; j++) {
        if (arrCols[j] == 0) return;
        if (hide) {
            table.querySelector("th:nth-child(" + arrCols[j] + ")").classList.add("hidden-column");
        } else { 
            table.querySelector("th:nth-child(" + arrCols[j] + ")").classList.remove("hidden-column");
        }
        let cells = table.querySelectorAll("tbody tr td:nth-child(" + arrCols[j] + ")");
        if (cells.length > 0) observer.disconnect();
        for (let i = 0; i < cells.length; i++) {
            if (hide) {
                cells[i].classList.add("hidden-column");
            } else { 
                cells[i].classList.remove("hidden-column");
            }
        }
    }
}
let options = {
        childList: true,
        subtree: true,
    },
    observer = new MutationObserver(initGrid);
observer.observe(table, options);
initGrid();
```

## Page Setup
1. Drag a *DataGrid* control to the page ([see above](#database-connector-and-datagrid))
2. Add a class of your choosing to the *DataGrid* *Classes* property that uniquely identifies this DataGrid on this page (e.g datagrid-hide-cols)

## Event Handler Setup
1. Populate your DataGrid with data ([see above](#database-connector-and-datagrid))
2. To hide or show columns when the event script runs
   1. Drag a *List* action into the script (type: Any)
   2. Add the column numbers you want to show or hide to the List

List Value Example:
```json
= [1,3]
```

3. Drag the *ColumnHiding* script into the script and complete the input parameters
   1. Action: Leave blank to hide columns. To show columns, add "Show"
   2. ColumnNumbers: Select your *List* containing the column numbers from the dropdown
   3. DataGridClass: The unique class you assigned to the *DataGrid* (e.g datagrid-hide-cols)
