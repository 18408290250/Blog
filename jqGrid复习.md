JqGrid
## 引入

## 隐藏横向滚动条CSS
```
#gbox_Grid .ui-jqgrid-bdiv {
            overflow-x: hidden!important; /*隐藏jqgrid横向滚动条*/
        }  
```

## 初始化JqGrid
funcPerAgent_Old -> perServiceIndex.html


## loadonce
funcPerAgent_Old -> perServiceIndex.html

loadonce: true
jqGrid只会加载一次数据，并把datatype改为local，以后不再请求后台
注意：刷新的时候(trigger('reloadGrid'))重新将datatype重新定位到json，也就是指向服务器后，jqGrid就会像服务器发起请求
            
## shrinkToFit
funcPerAgent_Old -> perServiceIndex.html

true，则按比例初始化列宽度。 
false，则列宽度使用 colModel指定的宽度


## 固定列
funcPerAgent_Old -> perServiceIndex.html
```
frozen: true
```

## 隐藏列
funcPerAgent_Old -> perServiceIndex.html

```
hidden: true
```

## grid大小随窗口变化而变化
```
 $(window).resize(function () {
   gridWidth = $(window).width();
   gridHeight = $(window).height();
   $("#Grid").setGridWidth(gridWidth * .98).setGridHeight(gridHeight - 90);
});
```

## 格式化
funcPerAgent_Old -> perServiceIndex.html

editoptions、searchoptions

// select
// 将后台取回的zonecode自动匹配到相应的zoneName;显示zoneName
```
 var myZoneName = ':;' + getjQGridOption("ZoneCode");

 { label: "存档机构", name: 'zonecode', width: 240, edittype: "select", 
formatter: "select", editoptions: { value: myZoneName }, stype: 'select', searchoptions: { value: myZoneName } }
```
```
{
	label: "出生日期", name: 'birthday', width: 93, stype: 'text', sortable: true, editoptions: {
	    dataInit: function (element) {
	        $(element).datepicker({
	            autoclose: true,
	            format: 'yyyy-mm-dd',
	            orientation: 'bottom',
	            todayHighlight: true
	
	        });
	    }
	}
}
```
或者
```
 { label: "版域级别", name: 'Lno', width: 70, editable: true, align: 'center', edittype: "select", formatter: "select", editoptions: { value: { '1': "省级", '2': '市州', '3': '区县' } } }
```

// checkbox  
sysManage_Old -> sysEdulevelAndDegree.html
```
{
     label: "是否有效", name: 'Ifvalid', width: 3, editable: true, editable: true, edittype: 'checkbox', formatter: 'checkbox',editoptions: { value: "1:0", defaultValue: "1" }
 }
```

// 限制Grid编辑时候的输入长度
```
editable: true, edittype: "text", editoptions: { maxlength: 20 }
```

// 格式化单元格内容为按钮
formatter:operation

```
 function operation(cellValue, options, row) {
            var op = "<a class='btn btn-primary btn-xs'  href='javascript:doChioce(" + row.PersonId + ")'>选&nbsp;&nbsp;&nbsp;择</a>";
            return op;
        }
```


// 编辑规则 editrules 
funcElecTrim_Old -> personDocContent.html
```
{ label: "大类", name: 'bigcat', width: -1, editrules: { required: true },editable: true, edittype: "select", formatter: "select", editoptions: { value: bigCatSelect, defaultValue: function () { return myBigCat} }, formoptions: {
                            label: "大类 <span style='color:red;font-size:14px;'>*</span>"
                                }
                        }
```

// 对form进行编辑时的属性设置
formoptions
funcTitle_Old -> TitleRegister.html
```
editoptions: { maxlength: 18 }, formoptions: { colpos: 1, rowpos: 1 }
```

// 根据某单元格内容的不同的状态改变背景颜色
funcElecTrim_Old -> personDocContent.html

```
loadComplete: function () {  setStateColor() ；}    

function setStateColor() {
    var ids = $('#Grid').getDataIDs();
    for (i = 0; i < ids.length; i++) {
        var rowData = $('#Grid').getRowData(ids[i]);
        if ($.trim(rowData.objstate) == '借出')
        {
            $('#Grid').setCell(i + 1, 'contents', '', { 'color': '#e45f13' });
        } 
        else if ($.trim(rowData.objstate) == '缺失')
            $('#Grid').setCell(i+1, 'contents', '', { 'color': 'red' });
    }          
}
```



## 顶部过滤搜索
funcPerAgent_Old -> perServiceIndex.html
```
// 放在初始化之后
$("#Grid").jqGrid('filterToolbar', { searchOprators: true }).jqGrid("setFrozenColumns");
```


## input过滤
funcPerAgent_Old -> perServiceIndex.html
```
<input type="text" class="form-control input-sm" id="unitname" name="unitname" value="" placeholder="至少4位或空值">
  
$("#unitname").on('keyup', function () {
            $(this).addSmartList('Unit', 2);
        });
```


## 重新加载数据
funcElecTrim_Old -> personDocContent.html

注意：需要同时更新多个Grid,在一个Grid的loadComplete事件中再更新另一个Grid

```
 	$("#Grid2").jqGrid('setGridParam', {
            url: '/funcPerAgent/perServiceIndex.ashx?myOpt=Grid2',
            postData: {
                PersonId: PersonId,
                HuJiId: sHuJiId,
                DocId: sDocId,
                partyid: spartyId
            },
            datatype: 'json',
            loadComplete: function () {
                obj(); // 另外一个Grid的刷新reload方法
            }
        }).trigger("reloadGrid");
```

## 页脚
funcElecTrim_Old -> personDocContent.html

```
pager: "#Page"
```
```
 $("#Grid").navGrid("#Page",
        { edit: true, add: true, del: true, search: false, refresh: false, searchtext: "查找", addtext: "添加", edittext: "编辑", deltext: "删除", refreshtext: "刷新" },{// edit},{// add },{// del},{// serach})
```
或
funcDocInOut_Old -> DocInList.html
```
//jqGrid增删改查
function gridAdd() {
    helper.layer.openPage('新增档案转入', _urlEdit, function () { reloadGrid(); });
}
function gridEdit() {
    var bizId = $("#Grid").getCell(_selectedRowId, "docInId");
    helper.layer.openPage('编辑档案转入', _urlEdit + '&bizId=' + bizId, function () { reloadGrid(); });
}
function gridDel() {
    alert("暂不支持删除功能");
}
$("#Grid").navGrid("#Page", {
    add: false, addtext: "新增", addfunc: gridAdd,
    edit: false, edittext: "编辑", editfunc: gridEdit,
    del: false, deltext: "删除", delfunc: gridDel,
    search: true, searchtext: "查找",
    refresh: false,
    view: false,
    align: "left"
},
{},//set add options
{},//edit add options
{},//del add options
{ multipleSearch: true, multipleGroup: false });
```


// 1.edit，add,del 向后台传递的额外数据可用editData实现
见：funcElecTrim_Old -> personDocContent.html
```
editData：{}
```

// 2.不在jqGrid初始化设置editurl，在每一个eidt,add……的函数单独设置url
见： sysManage_Old -> userRoleSet.html
```
url:
```


// 3.beforeInitData事件可动态修改列属性
```
 $(this).jqGrid('setColProp', 'modifyloginid', { editable: true });
```
// 4.afterSubmit 接受服务器的状态信息

// 5.afterShowForm


## 自定义页脚事件
 
funcElecTrim_Old -> personDocContent.html
```
$("#Grid").navGrid("#Page",{})
.navButtonAdd('#Page',
           {
               caption: "使用默认目录", buttonicon: "glyphicon-asterisk", onClickButton: DefaultSetting, position: "last"
           })
```


funcHuJi_Old -> HuJiInInfo.html
```
$("#Grid").navGrid("#Page",
{ edit: false, add: false, del: false, search: true, refresh: false,searchtext: "查找" }, {}, {}, {}, { multipleSearch: true });

$("#Grid").navButtonAdd("#Page",
   {
       caption: "新增挂靠", buttonicon: "glyphicon glyphicon-plus", onClickButton: HuJiAdd, position: "last"
   }).navButtonAdd("#Page",
   {
       caption: "修改挂靠", buttonicon: "glyphicon glyphicon-edit", onClickButton: checkHuJiAdd, position: "last"
   }).navButtonAdd("#Page",
   {
       caption: "减少户籍", buttonicon: "glyphicon glyphicon-trash", onClickButton: checkHuJiOut, position: "last"
   }).navButtonAdd("#Page",
   {
       caption: "家庭成员", buttonicon: "glyphicon glyphicon-user", onClickButton: familyMember, position: "last"
   }).navButtonAdd("#Page",
   {
       caption: "工作联系函", buttonicon: "glyphicon glyphicon-book", onClickButton: workContactOrder, position: "last"
   });
```

## 分页
funcPerAgent_Old -> perServiceIndex.html

rowNum: 50   -若条目总数大于rowNum自动前台分页，分页信息显示在页脚正中间
viewrecords: true -显示总的条目数，显示在页脚的右边




## 全文搜索内容
funcUnitAgent_Old -> UnitList.html

```
 var timer;
            $("#search_cells").on("keyup", function () {
                var self = this;
                if (timer) { clearTimeout(timer); }
                timer = setTimeout(function () {
                    //timer = null;
                    $("#Grid").jqGrid('filterInput', self.value);
                }, 0);
            });
```

## jqGrid导出
funcUnitAgent_Old -> UnitList.html

```
function ExportGridDataToExcel() {
            $("#Grid").jqGrid("exportToExcel", {
                includeLabels: true,
                includeGroupHeader: true,
                includeFooter: true,
                fileName: "单位列表.xlsx",
                maxlength: 40, // maxlength for visible string data 
                title: 'jqGrid Export to Excel',
                orientation: 'portrait',
                pageSize: 'A4',
                description: '描述文字',
                customSettings: null,
                download: '下载'
            })
        };
```




## jqGrid向后台自定义post传递数据
funcElecTrim_Old -> personDocContent.html

使用 postData 属性


## 分组 
funcElecTrim_Old -> personDocContent.html
```
grouping: true,
groupingView: {
    groupField: ["bigcat"],
    groupColumnShow: [true],
    groupText: ["<b>{0}</b>"],
    groupOrder: ["asc"],
    groupSummary: [false],
    groupCollapse: false
}
```


## 多选
```
 multiselect: true,

delData: {
            IID: function () {
                var sel_ids = $('#Grid').jqGrid('getGridParam', 'selarrrow');
                var IIDs = [];
                for (var i = 0; i < sel_ids.length; i++) {
                    var IID = $("#Grid").jqGrid("getCell", sel_ids[i], 'ADDNO');
                    IIDs.push(IID);
                }
                return IIDs.join(',').toString();
            }
        }
```





## 事件 
funcElecTrim_Old -> personDocContent.html

getGridParam  
getRowData
loadComplete
onSelectRow
afterSubmit
afterShowForm







