# front-07ex2. AG-Grid with Angular
________________________________________
## 1. AG-Grid with Angular
________________________________________
概要

```ts
// import { ActivatedRoute } from '@angular/router';
// import { FormGroup, FormBuilder } from '@angular/forms';
import { GridOptions, GridApi, ColGroupDef, ColDef, ColumnApi } from 'ag-grid-community';

  // public form: FormGroup;

  public gridOptions: GridOptions;
  private gridApi: GridApi;
  private gridColumnApi: ColumnApi;
  
  private dataSource: FooEntity[];
  
  constructor(
    // private route: ActivatedRoute,
    // private fb: FormBuilder,
  ) {
    // this.setupForm();
    this.setupGrid();
  }
  
  public onGridReady(params) {
    this.gridApi = params.api;
    this.gridColumnApi = params.columnApi;
    this.gridApi.setRowData([]);
  }
  
  private setupGridOptions() {
    this.gridOptions = {} as GridOptions;
    this.gridOptions.rowHeight = 24;
    this.gridOptions.headerHeight = 24;
    this.gridOptions.animateRows = true;
    this.gridOptions.domLayout = 'autoHeight';
    this.gridOptions.rowSelection = 'multiple';
    this.gridOptions.suppressContextMenu = true;
    this.gridOptions.suppressDragLeaveHidesColumns = true;
    
    this.gridOptions.defaultColDef = {
      editable: false,
      filter: true,
      filterParams: { selectAllOnMiniFilter: true },
      menuTabs: ['filterMenuTab'],
      resizable: true,
      sortable: true,
    };
    this.gridOptions.columnDefs = getGridColumnDefs();
    // this.gridOptions.localeText = ...
    // this.gridOptions.sideBar = ...
  }
  
  private getGridColumnDefs(): (ColDef | ColGroupDef)[] {
    return [
      {
        headerName: '画像名',
        field: 'ImageName',
        width: 80,
        cellClass: () => ['required-cell', 'editable-cell'],
        editable: true,
        filter: 'agTextColumnFilter',
        pinned: 'left',
      },
      {
        headerName: '対象',
        field: 'Target',
        width: 80,
        cellRenderer: params => {
          var input = document.createElement('input');
          input.type = "checkbox";
          input.checked = params.value;
          input.addEventListener('change', function(event) {
            params.data.Target = input.checked;
          });
          return input;
        },
        editable: true,
        filter: false,
        pinned: 'right',
        resizable: false,
      }
      // ...
    ];
  }
    
  private getData() {
    // ...
    this.gridApi.setRowData(this.dataSource);
  }
  
  private updateDataSource() {
    this.gridApi.stopEditing();
    // ...
    for(let item of this.dataSource) {
      item.Property1 = "...";
      item.Property2 = "...";
    }
    // ...
    this.gridApi.refreshCells();
  }
  
  private removeGridRows() {
    const selecteds = this.gridApi.getSelectedRows();
    for (let item of selecteds) {
      item.RemovedByUI = true;
    };
    this.gridApi.updateRowData({ remove: selecteds });
  }
  
  private addGridRow() {
     let a = new FooEntity();
     this.dataSource.push(a);
     this.gridApi.updateRowData({ add: [a] });
  }
```

要点

```text
1. GridOptions、onGridReady(params) を用意してテンプレートのag-grid-angularとバインド
2. onGridReadyの引数から、gridApi、gridColumnApiを取得
3. setRowData(any[])でバインド
4. ColDefのfieldにdataSourceと対応付けて、バインディングする
5. UIから値を変更した場合にdatasourceも変わる（双方向バインディング）
    https://www.ag-grid.com/javascript-data-grid/data-update/
    https://www.ag-grid.com/javascript-data-grid/cell-editing/
    ※ ver23からはupdateRowData()は非推奨、代わりにapplyTransaction()になった
    旧バージョンのリファレンスはこちら
    http://54.222.217.254/javascript-grid-data-update/
6. dataSourceで値を変えた後は、refreshCells()を呼び出してUIに反映する必要がある
    https://www.ag-grid.com/javascript-data-grid/data-update/
    https://www.ag-grid.com/javascript-data-grid/view-refresh/
7. 後からdataSourceに要素を追加や削除しても、AG-Grid側には反映されない
    - updateRowData({add: [item]}), dataSource.push(item)の両方をする必要がある
    - updateRowData({remove: [item]}), item.RemovedByUI = trueの両方をする必要がある
8. セルのbooleanとcheckboxのバインドは直接サポートされない
    - cellRendererに自前でaddEventListenerされたInputElementを用意して返すことで実現する
```