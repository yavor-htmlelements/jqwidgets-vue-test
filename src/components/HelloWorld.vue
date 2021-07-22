<template>
    <div style="font-size: 13px; font-family: Verdana; float: left">
        <JqxGrid ref="myGrid"
                 :width="getWidth" :source="dataAdapter" :columns="columns" :editable="true"
                 :showstatusbar="true"
                 :renderstatusbar="createButtonsContainers" 
                 :showaggregates="true"
                 :sortable="true" :altrows="true" :selectionmode="'multiplecellsadvanced'">
        </JqxGrid>
        <div style="margin-top: 20px">
      
            <div style="float: left; margin-left: 10px">
                <JqxButton @click="csvBtnOnClick()">Export to CSV</JqxButton>
            </div>
        </div>
        <br><br><br>
        <div>

        </div>
    </div>
</template>
<script>
    import JqxGrid from "jqwidgets-scripts/jqwidgets-vue/vue_jqxgrid.vue";
    import JqxButton from "jqwidgets-scripts/jqwidgets-vue/vue_jqxbuttons.vue";
    import JqxTreeGrid from 'jqwidgets-scripts/jqwidgets-vue/vue_jqxtreegrid.vue';
  
    export default {
        components: {
            JqxGrid,
            JqxButton,
            JqxTreeGrid
        },
        data: function () {
            return {
                getWidth: 800,
                // eslint-disable-next-line 
                dataAdapter: new jqx.dataAdapter(this.source),
                columns2: [
                { text: 'NavItem', dataField: 'NavItem', cellsRenderer: this.cellRenderNavItem, width: 208 },
                { text: 'filterIcon', dataField: 'filterIcon', width: 24 },
                ],
                columns: [
                    { text: 'id', datafield: 'id', width: 120,
                    
                    },
                    { text: 'Name', datafield: 'firstname', width: 120,  columntype: 'combobox',
                         createeditor: (row, column, editor) => {
                            // assign a new data source to the combobox.
                            let list = ['Stuttgart', 'Rio de Janeiro', 'Strasbourg'];
                            editor.jqxComboBox({ autoDropDownHeight: true, source: list, promptText: 'Pl:' });
                        },
                        // update the editor's value before saving it.
                        cellvaluechanging: (row, column, columntype, oldvalue, newvalue) => {
                            // return the old value, if the new value is empty.
                            if (newvalue == '') return oldvalue;
                        }
                      },
                    { text: 'Last Name', datafield: 'lastname', width: 120 },
                    { text: 'Product', datafield: 'productname', width: 180 },
                    { text: 'Quantity', datafield: 'quantity', width: 80, cellsalign: 'right', aggregates: ['min', 'max'] },
                    { text: 'Unit Price', datafield: 'price', width: 90, cellsalign: 'right', cellsformat: 'c2',  aggregates: ['sum', 'avg'] },
                    { text: 'Total', datafield: 'total', cellsalign: 'right', cellsformat: 'c2' }
                ]
            }
        },
        beforeCreate: function () {

            var data = new Array();
            var firstNames =
            [
                "Andrew", "Nancy", "Shelley", "Regina", "Yoshi", "Antoni", "Mayumi", "Ian", "Peter", "Lars", "Petra", "Martin", "Sven", "Elio", "Beate", "Cheryl", "Michael", "Guylene"
            ];
            var lastNames =
            [
                "Fuller", "Davolio", "Burke", "Murphy", "Nagase", "Saavedra", "Ohno", "Devling", "Wilson", "Peterson", "Winkler", "Bein", "Petersen", "Rossi", "Vileid", "Saylor", "Bjorn", "Nodier"
            ];
            var productNames =
            [
                "Black Tea", "Green Tea", "Caffe Espresso", "Doubleshot Espresso", "Caffe Latte", "White Chocolate Mocha", "Cramel Latte", "Caffe Americano", "Cappuccino", "Espresso Truffle", "Espresso con Panna", "Peppermint Mocha Twist"
            ];
            var priceValues =
            [
                "2.25", "1.5", "3.0", "3.3", "4.5", "3.6", "3.8", "2.5", "5.0", "1.75", "3.25", "4.0"
            ];
            for (var i = 0; i < 200; i++) {
                var row = {};
                var productindex = Math.floor(Math.random() * productNames.length);
                var price = parseFloat(priceValues[productindex]);
                var id = -i;
                var quantity = 1 + Math.round(Math.random() * 10);
                row["firstname"] = firstNames[Math.floor(Math.random() * firstNames.length)];
                row["lastname"] = lastNames[Math.floor(Math.random() * lastNames.length)];
                row["productname"] = productNames[productindex];
                row["price"] = price;
                row["quantity"] = quantity;
                row["total"] = price * quantity;
                row["id"] = id;
                data[i] = row;
            }
            this.source =
            {
                localdata: data,
                datatype: "array",
                datafields:
                [
                    { name: 'id', type: 'id' },
                    { name: 'firstname', type: 'string' },
                    { name: 'lastname', type: 'string' },
                    { name: 'productname', type: 'string' },
                    { name: 'quantity', type: 'number' },
                    { name: 'price', type: 'number' },
                    { name: 'total', type: 'number' }
                ]
            };

        },
        mounted: function () {
           function setCellClassName(rowData) {
              if (rowData.id == "-1") {
                    return "yellowCell";
                }
           }
           console.log(this.columns)
          const that= this;

          for( let i = 0; i < this.columns.length; i ++){

            that.$refs.myGrid.setcolumnproperty(this.columns[i].datafield, 'cellclassname', function (row, column, value, data ){
                let cellClassName= setCellClassName(data);
                return cellClassName;
            });

          }
          

        },
      
        methods: {
  
            csvBtnOnClick: function () {
                this.$refs.myGrid.setcolumnproperty('firstname','hidden', true);
                this.$refs.myGrid.setcolumnproperty('lastname','hidden', true);
                this.$refs.myGrid.setcolumnproperty('total','cellsformat', 'n2');

                //Hide aggregates and statusbar before the grid is exported
                this.$refs.myGrid.showstatusbar = false;
                this.$refs.myGrid.showaggregates = false;

                this.$refs.myGrid.refresh();

                this.$refs.myGrid.exportdata('csv', 'jqxGrid');
                
                //Show aggregates and statusbar after the grid is exported
                this.$refs.myGrid.showstatusbar = true;
                this.$refs.myGrid.showaggregates = true;

                this.$refs.myGrid.refresh();

                this.$refs.myGrid.setcolumnproperty('firstname','hidden', false);
                this.$refs.myGrid.setcolumnproperty('lastname','hidden', false);
                this.$refs.myGrid.setcolumnproperty('total','cellsformat', 'c2');
              
            },
            createButtonsContainers: function (statusbar) {
                let buttonsContainer = document.createElement('div');
                buttonsContainer.style.cssText = 'overflow: hidden; position: relative; margin: 5px;';
                let addButtonContainer = document.createElement('div');
                let deleteButtonContainer = document.createElement('div');
                let reloadButtonContainer = document.createElement('div');
                let searchButtonContainer = document.createElement('div');
                addButtonContainer.id = 'addButton';
                deleteButtonContainer.id = 'deleteButton';
                reloadButtonContainer.id = 'reloadButton';
                searchButtonContainer.id = 'searchButton';

                addButtonContainer.innerText = "Status bar test"
                deleteButtonContainer.innerText = 'deleteButton';
                reloadButtonContainer.innerText = 'reloadButton';

                addButtonContainer.style.cssText = 'float: left; margin-left: 5px;';
                deleteButtonContainer.style.cssText = 'float: left; margin-left: 5px;';
                reloadButtonContainer.style.cssText = 'float: left; margin-left: 5px;';
                searchButtonContainer.style.cssText = 'float: left; margin-left: 5px;';
                buttonsContainer.appendChild(addButtonContainer);
                buttonsContainer.appendChild(deleteButtonContainer);
                buttonsContainer.appendChild(reloadButtonContainer);
                buttonsContainer.appendChild(searchButtonContainer);
                statusbar[0].appendChild(buttonsContainer);
            },
            createButtons: function() {
                let addButtonOptions = {
                    width: 80, height: 25, value: 'Add',
                    imgSrc: '../../../images/add.png',
                    imgPosition: 'center', textPosition: 'center',
                    textImageRelation: 'imageBeforeText'
                }
                 // eslint-disable-next-line 
                let addButton = jqwidgets.createInstance('#addButton', 'jqxButton', addButtonOptions);
                let deleteButtonOptions = {
                    width: 80, height: 25, value: 'Delete',
                    imgSrc: '../../../images/close.png',
                    imgPosition: 'center', textPosition: 'center',
                    textImageRelation: 'imageBeforeText'
                }
                 // eslint-disable-next-line 
                let deleteButton = jqwidgets.createInstance('#deleteButton', 'jqxButton', deleteButtonOptions);
                let reloadButtonOptions = {
                    width: 80, height: 25, value: 'Reload',
                    imgSrc: '../../../images/refresh.png',
                    imgPosition: 'center', textPosition: 'center',
                    textImageRelation: 'imageBeforeText'
                }
                 // eslint-disable-next-line 
                let reloadButton = jqwidgets.createInstance('#reloadButton', 'jqxButton', reloadButtonOptions);
                let searchButtonOptions = {
                    width: 80, height: 25, value: 'Find',
                    imgSrc: '../../../images/search.png',
                    imgPosition: 'center', textPosition: 'center',
                    textImageRelation: 'imageBeforeText'
                }
                 // eslint-disable-next-line 
                let searchButton = jqwidgets.createInstance('#searchButton', 'jqxButton', searchButtonOptions);
                // add new row.
                addButton.addEventHandler('click', () => {
                      // eslint-disable-next-line 
                    let datarow = generatedata(1);
                    this.$refs.myGrid.addrow(null, datarow[0]);
                });
                // delete selected row.
                deleteButton.addEventHandler('click', () => {
                    let selectedrowindex = this.$refs.myGrid.getselectedrowindex();
                    // let rowscount = this.$refs.myGrid.getdatainformation().rowscount;
                    let id = this.$refs.myGrid.getrowid(selectedrowindex);
                    this.$refs.myGrid.deleterow(id);
                });
                // reload grid data.
                reloadButton.addEventHandler('click', () => {
                     // eslint-disable-next-line 
                    this.$refs.myGrid.source = this.getAdapter();
                });
                // search for a record.
                searchButton.addEventHandler('click', () => {
                    this.$refs.myWindow.open();
                    this.$refs.myWindow.move(60, 60);
                });
            },
            findBtnOnClick: function() {
                this.$refs.myGrid.clearfilters();
                let searchColumnIndex = this.$refs.myDropDownList.selectedIndex;
                let datafield = '';
                switch (searchColumnIndex) {
                    case 0:
                        datafield = 'firstname';
                        break;
                    case 1:
                        datafield = 'lastname';
                        break;
                    case 2:
                        datafield = 'productname';
                        break;
                    case 3:
                        datafield = 'quantity';
                        break;
                    case 4:
                        datafield = 'price';
                        break;
                }
                  // eslint-disable-next-line 
                let searchText = this.$refs.myInput.val();
                 // eslint-disable-next-line 
                let filtergroup = new jqx.filter();
                let filter_or_operator = 1;
                let filtervalue = searchText;
                let filtercondition = 'contains';
                let filter = filtergroup.createfilter('stringfilter', filtervalue, filtercondition);
                filtergroup.addfilter(filter_or_operator, filter);
                this.$refs.myGrid.addfilter(datafield, filtergroup);
                // apply the filters.
                this.$refs.myGrid.applyfilters();
            },
            clearBtnOnClick: function() {
                this.$refs.myGrid.clearfilters();
            }
        }
    }
</script>
<style>
    .yellowCell{
        background-color: yellow;
    }
</style>