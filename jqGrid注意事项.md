https://blog.mn886.net/jqGrid/  多级表格
wiki:http://www.trirand.com/jqgridwiki/doku.php?id=wiki:jqgriddocs


### filterToolbar
filterToolbar：只有local数据才起作用，因为是本地数据过滤，即是 loadonce:true

'filterToolbar' 与 多选复选框 同时用 复选框会错位。解决办法：多选取消'filterToolbar

### loadonce:true
loadonce:true  分组后，若对数据增删查改，显示会有问题，解决办法：重绘Grid
```
function initGrid0() {
    // 重绘
	var parent = $("#table_list_0");
    $("#table_list_0").empty();// 清空表格内容
    $("<table id='Grid0'></table>").appendTo(parent);
    $("<div id='Page0'></div>").appendTo(parent);  // 再根据数据重新绘制表格

    var g1_pflag = {"":"待传","0":"已传","1":"已缴","2":"拒收","3":"退费","4":"请退","9":"开票"};
    var gll_pflag = ":;'':待传;0:已传;1:已缴;2:拒收;3:退费;4:请退;9:开票";
    $("#Grid0").jqGrid({
        url: Address + '/gridSearch0',
        editurl: null,
        styleUI: 'Bootstrap',
        datatype: "json",
        postData:{
            dtstart: $('#dtStart').val(),
            dtend: $('#dtEnd').val()
        },
        colModel: [
            {label: "单位名称", name: 'uname', width: -1, editable : false},
            {label: "单位代码", name: 'UID', width: 40,  editable : false},
            {label: "费用类型", name: 'paytype',  width: 80,editable : false},
            {label: "金额", name: 'amount', width: 50, editable : false,formatter: "number"},
            {label: "税额", name: 'tax', width: 50, editable : true ,edittype: "text", editoptions: { maxlength: 49 },editrules: { number: true },formatter: "number"},
            {label: "生成时间", name:"transtime",width: 50,editable : false},
            {label: "票种", name: 'Manner', width: 50,  editable : false},
            {label: "税种", name: 'taxtype', width: 50,  editable : false},
            {label: "状态", name: 'pflag', width: 50,  editable : false, formatter: "select", editoptions: { value: g1_pflag }, stype: 'select', searchoptions: { value: gll_pflag }},
            {label: "业务员", name: 'username', width: 50,  editable : false},
            {label: "", name: 'payID',hidden:true},
            {label: "", name: 'userid', hidden:true},
            {label: "", name: 'departid', hidden:true},
            {label: "", name: 'paytypeID', hidden:true},
            {label: "", name: 'paytypesubid', hidden:true},
            {label: "", name: 'chechNo', hidden:true}
        ],
        rowNum: 100,
        loadonce: true,
        shrinkToFit: true,
        multiselect: true,
        multiboxonly: true,
        width: gridWidth,
        height: gridHeight0,
        viewrecords: true,
        pager: "#Page0",
        sortname: 'uname',
        sortorder: 'asc',
        grouping: true,
        groupingView: {
            groupField: ["uname"],
            groupColumnShow: [true],
            groupText: ["<b>{0}</b>"],
            groupOrder: ["asc"],
            groupSummary: [false],
            groupCollapse: false
        },
        showSummaryOnHide: true,
        onSelectRow: function (rowid) {
            $("#Grid0").saveOldValues(rowid);
        },
        loadComplete: function ()  {
            setStateColor();
        }
    });
    $("#Grid0").jqGrid('filterToolbar', {searchOprators: true}).jqGrid("setFrozenColumns");
}
```
### loadonce:false 
loadonce:false -> 前台自动分页失效


### grouping
分组：后台需要将需要分组的值放在一起，即需要排序，因为是按后台的值顺序进行填充，若相同字段分组的值不在一起，则会分出现分错组的问题。
```
		// 后台需要 用'UnitName'排序返回
		sortname: 'UnitName',
        sortorder: 'asc',
        grouping: true,
        groupingView: {
            groupField: ["UnitName"],
            groupColumnShow: [true],
            groupText: ["<b>{0}</b>"],
            groupOrder: ["asc"],
            groupSummary: [false],
            groupCollapse: false
        },

```

### subGrid
```
 $("#Grid1").jqGrid({
        url: Address + '/gridSearch1',
        editurl: null,
        styleUI: 'Bootstrap',
        datatype: "json",
        postData:{
            dtstart: $('#dtStart').val(),
            dtend: $('#dtEnd').val()
        },
        colModel: [
            {label: "", name: 'AddNo',  hidden:true},
            {label: "", name: 'zonecode',hidden:true},
            {label: "", name: 'itemlink',hidden:true},
            // {label: "", name: 'itemlink',hidden:true,key:true},
            {label: "名称", name: 'UnitName', width: 180,editable : false},
            {label: "开票日期", name: 'invoicetime',  width: 80,editable : false},
            {label: "发票号No", name: 'chechNo', width: 100, editable : true,editoptions: { maxlength: 8 },editrules: { number: true }},
            {label: "票种", name: 'FeeType', width: 30, editable : false},
            {label: "金额",name:"amount", width: 50,editable : false,formatter: "number"},
            {label: "出票人",name:"accountant", width: 50,editable : false}
        ],
        rowNum: 100,
        loadonce: true,
        shrinkToFit: true,
        multiselect: true,
        multiboxonly: true,
        width: gridWidth,
        height: gridHeight1,
        viewrecords: true,
        pager: "#Page1",
        sortname: 'UnitName',
        sortorder: 'asc',
        // grouping: true,
        // groupingView: {
        //     groupField: ["UnitName"],
        //     groupColumnShow: [true],
        //     groupText: ["<b>{0}</b>"],
        //     groupOrder: ["asc"],
        //     groupSummary: [false],
        //     groupCollapse: false
        // },
        showSummaryOnHide: true,
        subGrid : true, 
		// 第一种 subGrid 限制在于：后台回来的json数据格式，不能依靠name匹配
        /* subGridUrl : Address +'/SubGrid1', // 默认会带上id,值为父表格的key为true的列值；若需要额外参数见下：使用params
         subGridModel: [
             {
                 name  : ['货物或应税劳务、服务名称', '单位', '数量', '金额', '税率',"税"], // 标题列
                 width : [200, 200, 80, 80, 80,80],
                 params: ['itemlink']
             }
         ],*/
        // subGridOptions:{
        //     // plusicon : "glyphicon glyphicon-plus",
        //     // minusicon : "glyphicon glyphicon-thumbs-up",
        //     // openicon: "glyphicon glyphicon-retweet",
        //     expandOnLoad: true,
        //     selectOnExpand : true,
        //     reloadOnExpand : true
        // },
		// 第二种 subGrid
        subGridRowExpanded : function(subgrid_id, row_id) {
            // we pass two parameters
            // subgrid_id is a id of the div tag created whitin a table data
            // the id of this elemenet is a combination of the "sg_" + id of the row
            // the row_id is the id of the row
            // If we wan to pass additinal parameters to the url we can use
            // a method getRowData(row_id) - which returns associative array in type name-value
            // here we can easy construct the flowing
            var subgrid_table_id, pager_id;
            subgrid_table_id = subgrid_id + "_t";
            pager_id = "p_" + subgrid_table_id;
            var desired_width = $("#Grid1").width();
            desired_width -= 100;  // adjust this value as needed

            var rowData = $(this).getRowData(row_id);
            var uid = rowData['itemlink'];
            $("#" + subgrid_id).html("<table id='" + subgrid_table_id + "' class='scroll'></table><div id='" + pager_id + "' class='scroll'></div>");
            jQuery("#" + subgrid_table_id).jqGrid({
                url : Address +"/SubGrid1?itemlink=" + uid,
                datatype : "json",
                styleUI: 'Bootstrap',
                colModel : [
                    {label: "货物或应税劳务、服务名称", name: 'paytype', width: 100,editable : false},
                    {label: "单位", name: 'payunit',  width: 80,editable : false},
                    {label: "数量", name: 'countno',  width: 80,editable : false,formatter: "number"},
                    {label: "金额", name: 'amount',  width: 80,editable : false,formatter: "number"},
                    {label: "税率", name: 'taxrate',  width: 80,editable : false,formatter: "number"},
                    {label: "税", name: 'tax',  width: 80,editable : false,formatter: "number"}
                ],
                rowNum : 200,
                shrinkToFit: true,
                // pager : pager_id,
                sortname : 'paytype',
                sortorder : "asc",
                width:desired_width,
                height : '100%'
            });
            jQuery("#" + subgrid_table_id).jqGrid('navGrid', "#" + pager_id, {edit : false, add : false, del : false});
        },
        onSelectRow: function (rowid) {
            $("#Grid1").saveOldValues(rowid);
        },
        gridComplete: function() {
            // 页面加载时展开SubGrid
            // var timeOut = 50;
            // var rowIds = $("#Grid1").getDataIDs();
            // $.each(rowIds, function (index, rowId) {
            //     setTimeout(function() {
            //         $("#Grid1").expandSubGridRow(rowId);
            //     }, timeOut);
            //     timeOut = timeOut + 200;
            // });
        }
    });

```



Tip1:
当loadonce:true时，subGrid和grouping同时使用，grouping可用，前台分页正常，但subGrid无法点击展开。
subGrid可用 或 分组可用；分组与subGrid不可一起用； 
怪异：但是若点击下一页后，subGrid和grouping可同时使用。 

此时，前台的搜索filterToolbar可用。

若去掉分组，subGrid和其余都正常
未找到解决办法，认为是subGrid与grouping冲突(loadonce:true)

Tip2:
当loadonce:false时，subGrid和grouping同时使用，且功能正常。
但是前台分页功能失效，filterToolbar失效(因为是服务端数据)

若loadonce:true
目前解决：为保证 前台搜索，分页功能正常
grouping与subGrid使用其中一个



### 行内编辑
传给后台的值extraparam里面的值，应不可编辑；若非要可编辑，改为form形式编辑。
注意：传给后台的值，若可编辑；会出现 传过去的值是一个标签
```
 $("#Grid").jqGrid("inlineNav","#Page",{
        edit:(slevel == "1")?true:false,
        edittext:"编辑",
        add:(slevel == "1")?true:false,
        addtext:"新增",
        save:(slevel == "1")?true:false,
        savetext:"保存",
        cancel:(slevel == "1")?true:false,
        canceltext:"取消",
        editParams:{
            keys:false,
            oneditfunc:null,
            successfunc:function (val) {
                if($.parseJSON(val.responseText).data != "更新失败"){
                    $(this).jqGrid('setGridParam', { datatype: 'json' }).trigger('reloadGrid');
                }
            },
            url:Address + '/gridEdit',
            extraparam:{
                AddNo:function () {
                    var sel_id = $("#Grid").jqGrid('getGridParam', 'selrow');
                    var value = $("#Grid").jqGrid('getCell', sel_id, 'AddNo');
                    return value;
                },
                oldlist: function () {
                    return jqOldValues;
                }
            },
            aftersavefunc:null,
            errorfunc:null,
            afterrestorefunc:null,
            restoreAfterError:true,
            mtype:"POST"
        },
        addParams:{
            useDefValues:true,
            addRowParams:{
                keys:true,
                url:Address + '/gridAdd',
                extraparam:{},
                oneditfunc:null,
                successfunc:function (val) {
                    if($.parseJSON(val.responseText).data != "新增失败"){
                        $(this).jqGrid('setGridParam', { datatype: 'json' }).trigger('reloadGrid');
                    }
                }
            }
        }
    });
    // 添加删除按钮
    if(slevel == "1")
            $("#Grid").navButtonAdd("#Page",
        {
            caption: "删除", buttonicon: "glyphicon glyphicon-trash", onClickButton: doDel, position: "last"
        });
```

### 导出
```
var d =  $('#dtStart').val();
       d = new Date(d);
       d =  d.getFullYear() + '年' + (d.getMonth() + 1) + '月';
        $("#Grid").jqGrid("exportToExcel", {
            includeLabels: true,
            includeGroupHeader: true,
            includeFooter: true,
            fileName: d+"账户明细.xlsx",
            maxlength: 80, // maxlength for visible string data
            title: 'jqGrid Export to Excel',
            orientation: 'portrait',
            pageSize: 'A4',
            description: '账户明细',
            customSettings: null,
            download: '下载'
        })
```

### 格式化
当需要在filterTool上再加一个空的选项
```
    var g1_pflag = {"":"待传","0":"已传","1":"已缴","2":"拒收","3":"退费","4":"请退","9":"开票"};
    var gll_pflag = ":;'':待传;0:已传;1:已缴;2:拒收;3:退费;4:请退;9:开票";

{label: "状态", name: 'pflag', width: 50,  editable : false, formatter: "select", editoptions: { value: g1_pflag }, stype: 'select', searchoptions: { value: gll_pflag }},
```

### 分栏与统计
统计
footerrow:true
注意：一般放在gridCompele里面，否则，两个统计数据再统计值不正确

分栏：
setGroupHeaders

```
$("#Grid").jqGrid({
        url: Address + '/gridSearch',
        editurl: null,
        styleUI: 'Bootstrap',
        datatype: "json",
        postData:{
            dtstart: $('#dtStart').val(),
            dtend: $('#dtEnd').val()
        },
        colModel: [
            {label: "单位名称", name: 'UName', width: 280, editable : false,frozen: true},
            {label: "预算月份", name: 'outtime', width: 100,  editable : false,frozen: true},
            {label: "工资", name: 'InWage',  width: 100,editable : false,formatter: "number"},
            {label: "工资(财务)", name: 'InWageCai', width: 100, editable : false,formatter: "number"},
            {label: "工资(开票)", name: 'InWageCheck', width: 100, editable : false ,formatter: "number"},
            {label: "社保", name:"InSocial",width: 100,editable : false,formatter: "number"},
            {label: "社保(财务)", name: 'InSocialCai', width: 100,  editable : false,formatter: "number"},
            {label: "社保(开票)", name: 'InSocialCheck', width: 100,  editable : false,formatter: "number"},
            {label: "公积金", name: 'InHouse', width: 100,  editable : false, formatter: "number"},
            {label: "公积金(财务)", name: 'InHouseCai', width: 100,  editable : false,formatter: "number"},
            {label: "公积金(开票)", name: 'InHouseCheck', width: 100,  editable : false,formatter: "number"},
            {label: "社保退费", name: 'RetSocial', width: 100,  editable : false,formatter: "number"},
            {label: "其它代收", name: 'InOther', width: 100,  editable : false,formatter: "number"},
            {label: "其它代收(财务)", name: 'InOtherCai', width: 100,  editable : false,formatter: "number"},
            {label: "其它代收(开票)", name: 'InOtherCheck', width: 100,  editable : false,formatter: "number"},
            {label: "工资发放", name: 'OutWage', width: 100,  editable : false,formatter: "number"},
            {label: "工资发放(财务)", name: 'OutWageCai', width: 100,  editable : false,formatter: "number"},
            {label: "养老上缴", name: 'OutOld', width: 100,  editable : false,formatter: "number"},
            {label: "四险上缴", name: 'OutSocial', width: 100,  editable : false,formatter: "number"},
            {label: "公积金上缴", name: 'OutHouse', width: 100,  editable : false,formatter: "number"},
            {label: "其它支出", name: 'OutOther', width: 100,  editable : false,formatter: "number"},
            {label: "工资余额",name: "blanWage", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InWage == null ? 0:rowObject.InWage) - parseFloat(rowObject.OutWage == null ? 0:rowObject.OutWage).toFixed(2);
                }},
            {label: "工资余额(财务)",name: "blanWageCai", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InWageCai == null ? 0:rowObject.InWageCai) - parseFloat(rowObject.OutWageCai == null ? 0:rowObject.OutWageCai).toFixed(2);
                }},
            {label: "工资余额(开票)",name: "blanWageCheck", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InWageCheck == null ? 0:rowObject.InWageCheck) - parseFloat(rowObject.OutWageCai == null ? 0:rowObject.OutWageCai).toFixed(2);
                }},
            {label: "社保余额",name: "blanSocial", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InSocial == null ? 0:rowObject.InSocial ) + parseFloat(rowObject.RetSocial == null ? 0:rowObject.RetSocial ) - parseFloat(rowObject.OutSocial == null ? 0:rowObject.OutSocial ).toFixed(2);
                }},
            {label: "社保余额(财务)",name: "blanSocialCai", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InSocialCai == null ? 0:rowObject.InSocialCai ) + parseFloat(rowObject.RetSocial == null ? 0:rowObject.RetSocial ) - parseFloat(rowObject.OutSocial == null ? 0:rowObject.OutSocial ).toFixed(2);
                }},
            {label: "社保余额(开票)",name: "blanSocialCheck", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InSocialCheck == null ? 0:rowObject.InSocialCheck ) + parseFloat(rowObject.RetSocial == null ? 0:rowObject.RetSocial ) - parseFloat(rowObject.OutSocial == null ? 0:rowObject.OutSocial ).toFixed(2);
                }},
            {label: "公积金余额",name: "blanHouse", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InHouse  == null ? 0:rowObject.InHouse ) - parseFloat(rowObject.OutHouse  == null ? 0:rowObject.OutHouse ).toFixed(2);
                }},
            {label: "公积金余额(财务)",name: "blanHouseCai", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InHouseCai  == null ? 0:rowObject.InHouseCai ) - parseFloat(rowObject.OutHouse  == null ? 0:rowObject.OutHouse ).toFixed(2);
                }},
            {label: "公积金余额(开票)",name: "blanHouseCheck", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InHouseCheck  == null ? 0:rowObject.InHouseCheck ) - parseFloat(rowObject.OutHouse  == null ? 0:rowObject.OutHouse ).toFixed(2);
                }},
            {label: "其它余额",name: "blanOther", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InOther  == null ? 0:rowObject.InOther ) - parseFloat(rowObject.OutOther  == null ? 0:rowObject.OutOther ).toFixed(2);
                }},
            {label: "其它余额(财务)",name: "blanOtherCai", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InOther  == null ? 0:rowObject.InOtherCai ) - parseFloat(rowObject.OutOtherCai  == null ? 0:rowObject.OutOther ).toFixed(2);
                }},
            {label: "其它余额(开票)",name: "blanOtherCheck", align: "right", sorttype: "number",
                formatter: function (cellValue, option, rowObject) {
                    return parseFloat(rowObject.InOther  == null ? 0:rowObject.InOtherCheck ) - parseFloat(rowObject.InOtherCheck  == null ? 0:rowObject.OutOther ).toFixed(2);
                }},
            {label: "", name: 'ContractNumber', hidden:true}
        ],
        rowNum: 100,
        loadonce: true,
        shrinkToFit: false,
        multiselect: false,
        width: gridWidth,
        height: gridHeight,
        viewrecords: true,
        pager: "#Page",
        sortname: 'UName',
        sortorder: 'asc',
        footerrow:true, //开启统计
        onSelectRow: function (rowid) {
            $("#Grid").saveOldValues(rowid);
            sel_uid = $("#Grid").jqGrid('getCell', rowid, 'ContractNumber');
        },
        gridComplete:function () {
            if (!isLoaded == true){
                $("#Grid").jqGrid('setGroupHeaders', {
                    useColSpanStyle: true,
                    groupHeaders:[
                        {startColumnName: 'UName', numberOfColumns: 2, titleText: '单位、月份'},
                        {startColumnName: 'InWage', numberOfColumns: 13, titleText: '代收收入部分 (开票、到账)(业务、财务)'},
                        {startColumnName: 'OutWage', numberOfColumns: 6, titleText: '代收实支部分 (工资含税及税后扣除)(业务、财务)'},
                        {startColumnName: 'NetWage', numberOfColumns: 12, titleText: '代收月结余 (业务、财务)'}
                    ]
                });
                $("#Grid").jqGrid('filterToolbar', {searchOprators: true}).jqGrid("setFrozenColumns");
                isLoaded = true;
            }
            var t =  {
                "InWage": $(this).getCol("InWage",false,"sum"),
                "InWageCai":$(this).getCol("InWageCai",false,"sum"),
                "InWageCheck": $(this).getCol("InWageCheck",false,"sum"),
                "InSocial":$(this).getCol("InSocial",false,"sum"),
                "InSocialCai":$(this).getCol("InSocialCai",false,"sum"),
                "InSocialCheck":$(this).getCol("InSocialCheck",false,"sum"),
                "InHouse":$(this).getCol("InHouse",false,"sum"),
                "InHouseCai":$(this).getCol("InHouseCai",false,"sum"),
                "InHouseCheck":$(this).getCol("InHouseCheck",false,"sum"),
                "RetSocial":$(this).getCol("RetSocial",false,"sum"),
                "InOther":$(this).getCol("InOther",false,"sum"),
                "InOtherCai":$(this).getCol("InOtherCai",false,"sum"),
                "InOtherCheck":$(this).getCol("InOtherCheck",false,"sum"),
                "OutWage":$(this).getCol("OutWage",false,"sum"),
                "OutWageCai":$(this).getCol("OutWageCai",false,"sum"),
                "OutOld":$(this).getCol("OutOld",false,"sum"),
                "OutSocial":$(this).getCol("OutSocial",false,"sum"),
                "OutHouse":$(this).getCol("OutHouse",false,"sum"),
                "OutOther":$(this).getCol("OutOther",false,"sum"),
                "blanWage":$(this).getCol("blanWage",false,"sum"),
                "blanWageCai":$(this).getCol("blanWageCai",false,"sum"),
                "blanWageCheck":$(this).getCol("blanWageCheck",false,"sum"),
                "blanSocial":$(this).getCol("blanSocial",false,"sum"),
                "blanSocialCai":$(this).getCol("blanSocialCai",false,"sum"),
                "blanSocialCheck":$(this).getCol("blanSocialCheck",false,"sum"),
                "blanHouse":$(this).getCol("blanHouse",false,"sum"),
                "blanHouseCai":$(this).getCol("blanHouseCai",false,"sum"),
                "blanHouseCheck":$(this).getCol("blanHouseCheck",false,"sum"),
                "blanOther":$(this).getCol("blanOther",false,"sum"),
                "blanOtherCai":$(this).getCol("blanOtherCai",false,"sum"),
                "blanOtherCheck":$(this).getCol("blanOtherCheck",false,"sum")
            };
            $(this).jqGrid("footerData","set",t,false);
        },
        loadComplete: function ()  {
            sel_uid = "";
        }
    });

```

### 全文搜索
```
 var timer;
    $("#search_cells").on("keyup", function () {
        var self = this;
        if (timer) {
            clearTimeout(timer);
        }
        timer = setTimeout(function () {
            $("#Grid").jqGrid('filterInput', self.value);
        }, 0);
    });
```

### jqGrid 两个Grid 
一个grid选中，刷新另一个Grid
①：在第一个grid的onComplete里面让第二个Grid,reload.此时，js的顺序是先初始化第二个Grid，否则此时。reload第二个Gridd可能还未初始化完成，所以需要先初始化Grid1的结构。
```
$(document).ready(function () {
    var Address = "/funcFinance/BackSetting";
    var gridWidth = Math.floor(window.outerWidth * 0.53);
    var gridHeight = Math.floor(window.outerHeight * 0.5);
    var gridWidth1 = Math.floor(window.outerWidth * 0.36);
    var zone = ":;" + getjQGridOption("ZoneName");
    var data_label = ":;" + getjQGridOption("BankData");
    var t_zone = getCookie("CzoneCode");

    //================================================== Grid1 初始化 ====================================================
    $("#Grid1").jqGrid({
        url: null,
        editurl: null,
        styleUI: 'Bootstrap',
        datatype: "json",
        colModel: [
            {label: "Excel列名", name: 'ModelCol', width: 1,editable:true,editable:true,edittype: "text", editoptions: { maxlength: 19 }},
            {label: "数据列", name: 'DateCol', width: 1,editable:true,editable:true,edittype: "select", formatter: "select",stype:"select", editoptions: { value: data_label},searchoptions: { value:  data_label}},
            {label: "", name: 'zonecode', hidden:true,editable:false},
            {label: "", name: 'AddNo', hidden:true,editable:false},
            {label: "", name: 'Bid', hidden:true,editable:false}
        ],
        rowNum: 100,
        caption:"Excel列设置",
        loadonce: true,
        shrinkToFit:true,
        rownumbers:true,
        width: gridWidth1,
        height: gridHeight,
        sortname:'AddNo',
        sortorder: 'asc',
        viewrecords: true,
        multiselect: true, //多选
        multiboxonly: true,
        pager: "#Page1",
        gridComplete:function () {},
        loadComplete:function () {
            $("#Page1_right").remove();
            $("#Page1_center").remove();
        },
        onSelectRow: function (rowid) {
            $("#Grid1").saveOldValues(rowid);
        }
    });
    $("#Grid1")
        .navGrid("#Page1",{edit:false,add:false,del:false,search:true,searchtext:"查找",refresh:true,refreshtext:"刷新"},{},{},{},{multipleSearch: true})
        .inlineNav("#Page1",my_foot($("#Grid1")));
    var del_t_1 = function(){
        return doDel($("#Grid1"));
    }
    // 添加删除按钮
    $("#Grid1").navButtonAdd("#Page1",
        {
            caption: "删除", buttonicon: "glyphicon glyphicon-trash", onClickButton: del_t_1, position: "last"
        });

    //================================================== Grid 初始化 ====================================================

    $("#Grid0").jqGrid({
        url: Address + '/gridSearch0',
        editurl: null,
        styleUI: 'Bootstrap',
        datatype: "json",
        colModel: [
            {label: "", name: 'Bid', hidden:true, sortable: true,editable:false},
            {label: "银行名称", name: 'BankName', width: 2,editable:true,edittype: "text", editoptions: { maxlength: 49 }},
            {label: "数据开始行", name: 'FromRow', width: 1,editable:true,edittype: "text",editrules: {number:true,minValue:2}},
            {label: "模板名称(带后缀)", name: 'ModelName', width: 2,editable:true,edittype: "text", editoptions: { maxlength: 49 }},
            {label: "版域", name: 'zonecode', width: 2,editable:true,edittype: "select", formatter: "select",stype:"select", editoptions: { value: zone,defaultValue: t_zone},searchoptions: { value:  zone}}
        ],
        rowNum: 100,
        caption:"银行信息",
        loadonce: true,
        shrinkToFit: true,
        multiselect: true, //多选
        multiboxonly: true,
        sortname:'Bid',
        sortorder: 'asc',
        width: gridWidth,
        height: gridHeight,
        viewrecords: true,
        pager: "#Page0",
        onSelectRow: function (rowid) {
            $("#Grid0").saveOldValues(rowid);
            $("#Grid1").jqGrid('setGridParam', {
                url: Address + '/gridSearch1',
                datatype: 'json',
                postData: {
                    Bid: function () {
                        return $("#Grid0").jqGrid('getCell', rowid, 'Bid');
                    }
                }
            }).trigger("reloadGrid");
        },
        loadComplete:function () {
            $("#Grid0").jqGrid('setSelection', 1, true);
            $("#Page0_right").remove();
            $("#Page0_center").remove();
        }
    });
    $("#Grid0")
        .navGrid("#Page0",{edit:false,add:false,del:false,search:true,searchtext:"查找",refresh:true,refreshtext:"刷新"},{},{},{},{multipleSearch: true})
        .inlineNav("#Page0",my_foot($("#Grid0")));
    var del_t = function(){
        return doDel($("#Grid0"));
    };
    $("#Grid0").jqGrid('navGrid', "#Page0", {
        edit : false,
        add : false,
        del : false
    });
    $("#Grid0").navButtonAdd("#Page0",
        {
            caption: "删除", buttonicon: "glyphicon glyphicon-trash", onClickButton: del_t, position: "last"
        });

    //================================================== jqGrid大小随窗口变化 ====================================================

    $(window).resize(function () {
        gridWidth = Math.floor(window.outerWidth * 0.53);
        gridHeight = Math.floor(window.outerHeight * 0.5);
        gridWidth1 = Math.floor(window.outerWidth * 0.36);
        $("#Grid").setGridWidth(gridWidth).setGridHeight(gridHeight);
        $("#Grid1").setGridWidth(gridWidth1).setGridHeight(gridHeight);
    });


    //================================================== 页脚 =====================================================

    function my_foot(obj){
        var t_t = obj.attr("id");
        var t = parseInt(t_t.substr(t_t.length-1,1));
        return {
            edit:true,
            edittext:"编辑",
            add:true,
            addtext:"新增",
            save:true,
            savetext:"保存",
            cancel:true,
            canceltext:"取消",
            editParams:{
                keys:false,
                oneditfunc:null,
                successfunc:function (val) {
                    if($.parseJSON(val.responseText).data != "更新失败"){
                        if(t == 0){
                            reloadGrid0("");
                        }
                        else if(t == 1){
                            reloadGrid1();
                        }
                    }
                },
                url:Address + '/'+t_t+'Edit',
                extraparam:{
                    AddNo:function () {
                        var sel_id = obj.jqGrid('getGridParam', 'selrow');
                        var value = "";
                        if(t == 0){
                            value = obj.jqGrid('getCell', sel_id, 'Bid');
                        }
                        else if(t == 1){
                            value = obj.jqGrid('getCell', sel_id, 'AddNo');
                        }
                        return value;
                    },
                    oldlist: function () {
                        return jqOldValues;
                    }
                },
                aftersavefunc:null,
                errorfunc:null,
                afterrestorefunc:null,
                restoreAfterError:true,
                mtype:"POST"
            },
            addParams:{
                useDefValues:true,
                addRowParams:{
                    keys:true,
                    url:Address + '/'+t_t+ 'Add',
                    extraparam:{
                        Bid:function () {
                            var value = "";
                            var sel_id = $("#Grid0").jqGrid('getGridParam', 'selrow');
                            if(t == 1){
                                value = $("#Grid0").jqGrid('getCell', sel_id, 'Bid');
                            }
                            return value;
                        }
                    },
                    oneditfunc:null,
                    successfunc:function (val) {
                        if($.parseJSON(val.responseText).data != "新增失败"){
                            $(this).jqGrid('setGridParam', { datatype: 'json' }).trigger('reloadGrid');
                        }
                    }
                }
            }
        };
    }

    //================================================== 删除 =====================================================

    function  doDel(obj) {
        var t_t = obj.attr("id");
        var t = parseInt(t_t.substr(t_t.length-1,1));
        var IIDs = [];
        if (t == 0) {
            IIDs = con(obj,'Bid', ",");
        }else if(t == 1){
            IIDs = con(obj,'AddNo', ",");
        }
        if(IIDs.length == 0){
            RunPop('提示', '', "请选择记录", '提示', 0.4, 0.2);
            return;
        }
        RunPop('提示', '', "请确认是否删除？", '提示', 0.4, 0.2, function () {
            var d_t =  encodeURI(Address + '/'+t_t+ 'Del?IIDs='+IIDs);
            $.ajax({
                type: 'GET',
                url: d_t,
                dataType: "json",
                success: function (data) {
                    var d = data.data;
                    if(d && d.indexOf("成功") > -1){
                        RunPop('提示', '', "删除成功", '提示', 0.4, 0.2,'','zb');
                        if (t == 0) {
                            reloadGrid0(reloadGrid1(""));
                        }else if(t == 1){
                            reloadGrid1();
                        }
                    }
                },
                error:function () {
                }
            });
        },'za');
    }
    
    function reloadGrid0(fn) {
        $("#Grid0").jqGrid('setGridParam', {
            url: Address + '/gridSearch0',
            datatype: 'json',
            loadComplete:function () {
                $("#Grid0").jqGrid('setSelection', 1, true);
                if($.isFunction(fn)){
                    fn();
                }
            }
        }).trigger("reloadGrid");
    }
    function reloadGrid1() {
        var sel_id = $("#Grid0").jqGrid('getGridParam', 'selrow');
        var tbid = $("#Grid0").jqGrid('getCell', sel_id, 'Bid');
        if (tbid) {
            $("#Grid1").jqGrid('setGridParam', {
                url: Address + '/gridSearch1',
                datatype: 'json',
                postData: {
                    Bid: tbid
                }
            }).trigger("reloadGrid");
        }
    }


    function con(obj,key,op) {
        var sel_ids = obj.jqGrid('getGridParam', 'selarrrow');
        var IIDs = [];
        for (var i = 0; i < sel_ids.length; i++) {
            var IID = $.trim(obj.jqGrid("getCell", sel_ids[i], key));
            IIDs.push(IID);
        }
        return IIDs.join(op).toString();
    }

});

```
②：在第一个Grid得到数据后，初始化第二个Grid
```
$(document).ready(function () {
    var Address = "/funcFinance/BackSetting";
    var gridWidth = Math.floor(window.outerWidth * 0.53);
    var gridHeight = Math.floor(window.outerHeight * 0.5);
    var gridWidth1 = Math.floor(window.outerWidth * 0.36);
    var zone = ":;" + getjQGridOption("ZoneName");
    var data_label = ":;" + getjQGridOption("BankData");
    var t_zone = getCookie("CzoneCode");

    //================================================== Grid 初始化 ====================================================
    $("#Grid0").jqGrid({
        url: Address + '/gridSearch0',
        editurl: null,
        styleUI: 'Bootstrap',
        datatype: "json",
        colModel: [
            {label: "", name: 'Bid', hidden:true, sortable: true,editable:false},
            {label: "银行名称", name: 'BankName', width: 2,editable:true,edittype: "text", editoptions: { maxlength: 49 }},
            {label: "数据开始行", name: 'FromRow', width: 1,editable:true,edittype: "text",editrules: {number:true,minValue:2}},
            {label: "模板名称(带后缀)", name: 'ModelName', width: 2,editable:true,edittype: "text", editoptions: { maxlength: 49 }},
            {label: "版域", name: 'zonecode', width: 2,editable:true,edittype: "select", formatter: "select",stype:"select", editoptions: { value: zone,defaultValue: t_zone},searchoptions: { value:  zone}}
        ],
        rowNum: 100,
        caption:"银行信息",
        loadonce: true,
        shrinkToFit: true,
        multiselect: true, //多选
        multiboxonly: true,
        sortname:'Bid',
        sortorder: 'asc',
        width: gridWidth,
        height: gridHeight,
        viewrecords: true,
        pager: "#Page0",
        onSelectRow: function (rowid) {
            $("#Grid0").saveOldValues(rowid);
            // Grid0选中，刷新Grid1
            reloadGrid1();
        },
        loadComplete:function () {
            $("#Grid0").jqGrid('setSelection', 1, true);
            $("#Page0_right").remove();
            $("#Page0_center").remove();
            // Grid0 数据加载完成，加载相关的Grid1
            loadGrid1();
        }
    });
    $("#Grid0")
        .navGrid("#Page0",{edit:false,add:false,del:false,search:true,searchtext:"查找",refresh:true,refreshtext:"刷新"},{},{},{},{multipleSearch: true})
        .inlineNav("#Page0",my_foot($("#Grid0")));
    var del_t = function(){
        return doDel($("#Grid0"));
    };
    $("#Grid0").jqGrid('navGrid', "#Page0", {
        edit : false,
        add : false,
        del : false
    }).navButtonAdd("#Page0",
        {
            caption: "删除", buttonicon: "glyphicon glyphicon-trash", onClickButton: del_t, position: "last"
        });

    //================================================== Grid1 初始化 ====================================================
    function loadGrid1(){
        var rowid =  $("#Grid0").jqGrid('getGridParam', 'selrow');
        var bid =  $("#Grid0").jqGrid('getCell', rowid, 'Bid');
        $("#Grid1").jqGrid({
            url: Address + '/gridSearch1',
            editurl: null,
            styleUI: 'Bootstrap',
            datatype: "json",
            postData: {
                Bid: bid
            },
            colModel: [
                {label: "Excel列名", name: 'ModelCol', width: 1,editable:true,editable:true,edittype: "text", editoptions: { maxlength: 19 }},
                {label: "数据列", name: 'DateCol', width: 1,editable:true,editable:true,edittype: "select", formatter: "select",stype:"select", editoptions: { value: data_label},searchoptions: { value:  data_label}},
                {label: "", name: 'zonecode', hidden:true,editable:false},
                {label: "", name: 'AddNo', hidden:true,editable:false},
                {label: "", name: 'Bid', hidden:true,editable:false}
            ],
            rowNum: 100,
            caption:"Excel列设置",
            loadonce: true,
            shrinkToFit:true,
            rownumbers:true,
            width: gridWidth1,
            height: gridHeight,
            sortname:'AddNo',
            sortorder: 'asc',
            viewrecords: true,
            multiselect: true, //多选
            multiboxonly: true,
            pager: "#Page1",
            gridComplete:function () {},
            loadComplete:function () {
                $("#Page1_right").remove();
                $("#Page1_center").remove();
            },
            onSelectRow: function (rowid) {
                $("#Grid1").saveOldValues(rowid);
            }
        });
        $("#Grid1")
            .navGrid("#Page1",{edit:false,add:false,del:false,search:true,searchtext:"查找",refresh:true,refreshtext:"刷新"},{},{},{},{multipleSearch: true})
            .inlineNav("#Page1",my_foot($("#Grid1")));
        var del_t_1 = function(){
            return doDel($("#Grid1"));
        }
        // 添加删除按钮
        $("#Grid1").navButtonAdd("#Page1",
            {
                caption: "删除", buttonicon: "glyphicon glyphicon-trash", onClickButton: del_t_1, position: "last"
            });
    }

    //================================================== jqGrid大小随窗口变化 ====================================================
    $(window).resize(function () {
        gridWidth = Math.floor(window.outerWidth * 0.53);
        gridHeight = Math.floor(window.outerHeight * 0.5);
        gridWidth1 = Math.floor(window.outerWidth * 0.36);
        $("#Grid").setGridWidth(gridWidth).setGridHeight(gridHeight);
        $("#Grid1").setGridWidth(gridWidth1).setGridHeight(gridHeight);
    });

    //================================================== 页脚 =====================================================
    function my_foot(obj){
        var t_t = obj.attr("id");
        var t = parseInt(t_t.substr(t_t.length-1,1));
        return {
            edit:true,
            edittext:"编辑",
            add:true,
            addtext:"新增",
            save:true,
            savetext:"保存",
            cancel:true,
            canceltext:"取消",
            editParams:{
                keys:false,
                oneditfunc:null,
                successfunc:function (val) {
                    if($.parseJSON(val.responseText).data != "更新失败"){
                        if(t == 0){
                            reloadGrid0("");
                        }
                        else if(t == 1){
                            reloadGrid1();
                        }
                    }
                },
                url:Address + '/'+t_t+'Edit',
                extraparam:{
                    AddNo:function () {
                        var sel_id = obj.jqGrid('getGridParam', 'selrow');
                        var value = "";
                        if(t == 0){
                            value = obj.jqGrid('getCell', sel_id, 'Bid');
                        }
                        else if(t == 1){
                            value = obj.jqGrid('getCell', sel_id, 'AddNo');
                        }
                        return value;
                    },
                    oldlist: function () {
                        return jqOldValues;
                    }
                },
                aftersavefunc:null,
                errorfunc:null,
                afterrestorefunc:null,
                restoreAfterError:true,
                mtype:"POST"
            },
            addParams:{
                useDefValues:true,
                addRowParams:{
                    keys:true,
                    url:Address + '/'+t_t+ 'Add',
                    extraparam:{
                        Bid:function () {
                            var value = "";
                            var sel_id = $("#Grid0").jqGrid('getGridParam', 'selrow');
                            if(t == 1){
                                value = $("#Grid0").jqGrid('getCell', sel_id, 'Bid');
                            }
                            return value;
                        }
                    },
                    oneditfunc:null,
                    successfunc:function (val) {
                        if($.parseJSON(val.responseText).data != "新增失败"){
                            $(this).jqGrid('setGridParam', { datatype: 'json' }).trigger('reloadGrid');
                        }
                    }
                }
            }
        };
    }

    //================================================== 删除 =====================================================

    function  doDel(obj) {
        var t_t = obj.attr("id");
        var t = parseInt(t_t.substr(t_t.length-1,1));
        var IIDs = [];
        if (t == 0) {
            IIDs = con(obj,'Bid', ",");
        }else if(t == 1){
            IIDs = con(obj,'AddNo', ",");
        }
        if(IIDs.length == 0){
            RunPop('提示', '', "请选择记录", '提示', 0.4, 0.2);
            return;
        }
        RunPop('提示', '', "请确认是否删除？", '提示', 0.4, 0.2, function () {
            var d_t =  encodeURI(Address + '/'+t_t+ 'Del?IIDs='+IIDs);
            $.ajax({
                type: 'GET',
                url: d_t,
                dataType: "json",
                success: function (data) {
                    var d = data.data;
                    if(d && d.indexOf("成功") > -1){
                        RunPop('提示', '', "删除成功", '提示', 0.4, 0.2,'','zb');
                        if (t == 0) {
                            reloadGrid0(reloadGrid1(""));
                        }else if(t == 1){
                            reloadGrid1();
                        }
                    }
                },
                error:function () {
                }
            });
        },'za');
    }

    function reloadGrid0(fn) {
        $("#Grid0").jqGrid('setGridParam', {
            url: Address + '/gridSearch0',
            datatype: 'json',
            loadComplete:function () {
                $("#Grid0").jqGrid('setSelection', 1, true);
                if($.isFunction(fn)){
                    fn();
                }
            }
        }).trigger("reloadGrid");
    }
    function reloadGrid1() {
        var sel_id = $("#Grid0").jqGrid('getGridParam', 'selrow');
        var tbid = $("#Grid0").jqGrid('getCell', sel_id, 'Bid');
        if (tbid) {
            $("#Grid1").jqGrid('setGridParam', {
                url: Address + '/gridSearch1',
                datatype: 'json',
                postData: {
                    Bid: tbid
                }
            }).trigger("reloadGrid");
        }
    }


    function con(obj,key,op) {
        var sel_ids = obj.jqGrid('getGridParam', 'selarrrow');
        var IIDs = [];
        for (var i = 0; i < sel_ids.length; i++) {
            var IID = $.trim(obj.jqGrid("getCell", sel_ids[i], key));
            IIDs.push(IID);
        }
        return IIDs.join(op).toString();
    }

});


```

另，如果左边的Grid会根据条件刷新，需要在左边Grid记录为0的时候，清空右边Grid
```
if($("#Grid").jqGrid('getGridParam', 'records') == 0){
                $("#Grid1").jqGrid("clearGridData");
            }
```

### jqGrid  行页脚和form页脚
因为行编辑页脚没有搜索，所以如果需要在行编辑页脚加入navGrid的搜索。需要先设置navGrid,再写inlineNavGrid,否则没有效果。
```
$("#Grid1").navGrid("#Page1",{edit:false,add:false,del:false,search:true,searchtext:"查找",refresh:true,refreshtext:"刷新"},{},{},{},{multipleSearch: true})

$("#Grid1").jqGrid("inlineNav","#Page1",my_foot($("#Grid1")));
```

### jqGrid 设置新增可编辑，编辑不可编辑
使用行内编辑时，传过去的值不可以修改
如果需要新增可编辑，编辑不可编辑，需使用form修改数据，通过
```
 beforeInitData: function (formid) {   //设置主键在添加时可编辑
                $(this).jqGrid('setColProp', 'paytypeID', { editable: true });
            },

 beforeInitData: function (formid) { //设置主键在编辑时不可编辑
                $(this).jqGrid('setColProp', 'paytypeID', { editable: false });
            },
```

### 行内编辑  beforeSaveRow 可以在保存之前校验数据
返回true,jqGrid继续发送数到后台
返回false,jqGrid不发送数据到后台
```
editParams: {
    keys: false,
    oneditfunc: null,
    beforeSaveRow: function (options, rowid){
        var rowData = $(this).jqGrid('getRowData',rowid);
        if(rowData.applymarkH == "0" && rowData.applyokmarkH == '1'){
            RunPop('','','请先设定申报','',0.5,0.3,'','lj9');
            return false;
        }
    },
    successfunc: function (val) {
        if ($.parseJSON(val.responseText).data != "更新失败") {
            $(this).jqGrid('setGridParam', {datatype: 'json'}).trigger('reloadGrid');
        }
    }}

```

### jqGerid 自定义删除按钮，删除多行数据
```
var del_t = function(){
        return doDel($("#Grid0"));
    };
$("#Grid0").navButtonAdd("#Page0",
        {
            caption: "删除", buttonicon: "glyphicon glyphicon-trash", onClickButton: del_t, position: "last"
        });
function  doDel(obj) {
        var t_t = obj.attr("id");
        var t = parseInt(t_t.substr(t_t.length - 1, 1));
        var IIDs = [];
        if (t == 0) {
            IIDs = con(obj, 'Bid', ",");
        } else if (t == 1) {
            IIDs = con(obj, 'AddNo', ",");
        }
        if (IIDs.length == 0) {
            RunPop('提示', '', "请选择记录", '提示', 0.4, 0.2);
            return;
        }
        var d_t = encodeURI(Address + '/' + t_t + 'Del?IIDs=' + IIDs);
        var sel_id = obj.jqGrid("getGridParam", "selrow");
        obj.jqGrid("delGridRow", sel_id, {
            url: d_t,
            mtype: 'GET',
            afterShowForm: function (formid) {
                $('.EditButton .btn-default').addClass('btn-primary');
            },
            afterSubmit: function (response, postdata) {
                var d = $.parseJSON(response.responseText).data;
                if (d && d.indexOf("成功") > -1) {
                    if (t == 0) {
                        reloadGrid0(reloadGrid1(""));
                    } else if (t == 1) {
                        reloadGrid1();
                    }
                    return [true, ''];
                }
                return [false, '删除失败'];
            }
        });
    }

```

### jqGrid 动态数据 动态列
```
// 动态追加数据
var data = [];
data.push({'roleName':'普通员工','roleCode'});
data.push({'roleName':'普通员工','roleCode'});

// 动态生成列模型
var columnModel = [];
columnModel.push({header:'角色名称', name:'roleName', sortable:false, width:100});
columnModel.push({header:'角色编码', name:'roleCode', sortable:false, width:100});

// 生成表格
$("#roleGrid").dataGrid({
    data: data,  // 应用动态生成的数据
    datatype: "local",
    columnModel: columnModel, // 应用动态生成的列模型
    showCheckbox: false
});
```

### jqGrid 列表列格式化设置
```
// 金额、货币、数量、千分位显示
{header:'金额', name:'price', formatter:'integer', formatoptions:{thousandsSeparator:',', defaulValue:''}},
{header:'货币', name:'price', formatter:'currency', formatoptions:{thousandsSeparator:',', decimalSeparator:'.', decimalPlaces:2, defaulValue:'', prefix:'￥'}},  
{header:'数量',  name:'amount', formatter:'number', formatoptions:{thousandsSeparator:',', decimalPlaces:3, defaulValue:''}},
// 日期类型格式化（原格式：yyyy-MM-dd HH:mm:ss  转换为：yyyy-MM-dd HH:mm）
{header:'更新时间', name:'updateDate', formatter:"date", formatoptions:{srcformat:'Y-m-d H:i:s', newformat:'Y-m-d H:i'}},
```
formatter：主要是设置格式化类型(integer、email等以及函数来支持自定义类型)

formatoptions：用来设置对应 formatter 的参数，jqGrid 中预定义了常见的格式及其 options 如下：

integer
thousandsSeparator： //千分位分隔符,
defaulValue
number
decimalSeparator, //小数分隔符，如”.”
thousandsSeparator, //千分位分隔符，如”,”
decimalPlaces, //小数保留位数
defaulValue
currency
decimalSeparator, //小数分隔符，如”.”
thousandsSeparator, //千分位分隔符，如”,”
decimalPlaces, //小数保留位数
defaulValue,
prefix //前缀，如加上”$”
suffix//后缀
date
srcformat, //source的本来格式
newformat //新格式
email
没有参数，会在该cell是email加上： mailto:name@domain.com
showlink
baseLinkUrl, //在当前cell中加入link的url，如”jq/query.action”
showAction, //在baseLinkUrl后加入&action=actionName
addParam, //在baseLinkUrl后加入额外的参数，如”&name=aaaa”
target,
idName //默认会在baseLinkUrl后加入,如”.action?id=1”。改如果设置idName=”name”,那么”.action?name=1”。其中取值为当前rowid
checkbox
disabled //true/false 默认为true此时的checkbox不能编辑，如当前cell的值是1、0会将1选中
select
设置下拉框，没有参数，需要和colModel里的editoptions配合使用

### jqGrid  cellAttr
当fomtter被占用的时候，cellAttr可作为代替使用
这个方法在创建单元格内容的时候给单元格添加附加到属性。所有可用的单元格属性或者style属性中可用的值都可以使用，这个方法需要返回字符串。传递进入这个配置方法的参数如下：
rowId - 单元格所属行id；
val - 显示在单元格中的值；
rawObject -原始的数据行对象。如果jqGrid配置中datatype为json，则为array对象。datatype为xml时则为xml node节点；
cm - 所有colModel中配置的属于此列的属性；
rdata - the data row which will be inserted in the row.  参数为键值对对象数组，name为colModel定义的name