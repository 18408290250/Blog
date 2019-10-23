js解决打印表单时获取不到填写的数据问题

```
function printMe() {
    bindData();
    var oPop = window.open('', 'oPop');
    var sHtml = document.getElementById('prnMain').innerHTML;
    console.log(sHtml)
    // // // var sLink = '<style>body{padding:30px; margin:0; font-family:"Microsoft Yahei"; line-height:2em; font-size:16pt;}label { display: inline-block; margin: 10px 0; }.left{position:absolute; -webkit-print-color-adjust: exact; border: 2px dotted #000;left:2%; top:20%;width: 95%; height: 60%; font-size:16pt;background:#f4f4f4; }.tlt{ width: 150px; text-align: right; margin-left: 50px; text-align:right; }.mt100 { margin-top: 80px; }.ipt { margin-left:10px; }.hWord:after{ content:"元";}</style>'
    var str = sHtml;
    oPop.document.write(str);
    oPop.print();
    oPop.close();
}

//将表单中手动填写的数据进行绑定，便于html()的时候获取到
function bindData(){
    //搞定 type=text, 同时如果checkbox,radio,select>option的值有变化, 也绑定一下, 这里忽略button
    $("input,select option").each(function(){
        $(this).attr('value',$(this).val());
    });
    //搞定 type=checkbox,type=radio 选中状态
    $("input[type='checkbox'],input[type='radio']").each(function(){
        if($(this).attr('checked'))
            $(this).attr('checked',true);
        else
            $(this).removeAttr('checked');
    });
    //搞定select选中状态
    $("select option").each(function(){
        if($(this).attr('selected'))
            $(this).attr('selected',true);
        else
            $(this).removeAttr('selected');
    });
    //搞定 textarea
    $("textarea").each(function(){
        $(this).html($(this).val());
    });
}

```