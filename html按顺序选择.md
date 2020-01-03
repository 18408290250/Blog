按顺序选择一个，且下一个下拉不能选择上一个选择的

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1"/>
    <title></title>
    <link href="../css/bootstrap-min.css" rel="stylesheet"/>
    <link href="../css/bootstrap-datepicker.css" rel="stylesheet"/>
    <script src="../js/jquery-1.11.0.min.js"></script>
    <script src="../js/bootstrap-min.js"></script>
    <script src="../js/bootstrap-datepicker.min.js"></script>
    <style>
        .control-label {
            text-align: right;
        }
    </style>
</head>
<body>
<div class="container-fluid">
    <div class="row" style="margin-top: 2rem;">
        <form role="form" class="form-horizontal col-sm-offset-1 col-sm-10" id="fm">
            <h4>请按顺序填写志愿：</h4>
            <div class="form-group ex">
                <label class="control-label col-xs-2">第一志愿:</label>
                <div class="col-xs-4">
                    <select class="form-control input-sm s">
                    </select>
                </div>
            </div>
            <div class="form-group ex">
                <label class="control-label col-xs-2">第二志愿:</label>
                <div class="col-xs-4">
                    <select class="form-control input-sm s">
                        <option value="" disabled selected>请选择</option>
                    </select>
                </div>
            </div>
            <div class="form-group ex">
                <label class="control-label col-xs-2">第三志愿:</label>
                <div class="col-xs-4">
                    <select class="form-control input-sm s">
                        <option value="" disabled selected>请选择</option>
                    </select>
                </div>
            </div>
            <div class="form-group ex">
                <label class="control-label col-xs-2">第四志愿:</label>
                <div class="col-xs-4">
                    <select class="form-control input-sm s">
                        <option value="" disabled selected>请选择</option>
                    </select>
                </div>
            </div>
            <div class="form-group">
                <div class="col-xs-1 col-xs-offset-10">
                    <a href="javascript:;" class="btn btn-primary" id="hSearch" style="margin-left:-3rem;">提交</a>
                </div>
            </div>
        </form>
    </div>


</div>
</body>
<script>
    var oper = "select";
    $(document).ready(function () {
        $("#startTime").datepicker({
            autoclose: true,
            format: 'yyyy-mm-dd',
            orientation: 'top',
            todayHighlight: true

        });
        $("#endTime").datepicker({
            autoclose: true,
            format: 'yyyy-mm-dd',
            orientation: 'top',
            todayHighlight: true

        });


        $(".s").on("change", function () {
            var temp = $(this).val();
            $(this).parents('.ex').find(".inputex").attr("name", temp);
            $(this).parents('.ex').find(".inputex").removeAttr("id");
            var input = $(this).parent().siblings().find(".inputex");
            if (input.attr("name") == "graduateschool") {
                input.attr("id", "input1");
            }
            if (input.attr("name") == "unitname") {
                input.attr("id", "input2");
            }

        });

        $(".t").on("change", function () {
            var temp = $(this).val();
            $(this).parents('.ex').find(".inputT1").attr("name", temp + "_startTime");
            $(this).parents('.ex').find(".inputT2").attr("name", temp + "_endTime");
        });


        $(document).on("keyup", '#input1', function () {
            $(this).addSmartList('University', 2);
        })
        $(document).on("keyup", '#input2', function () {
            $(this).addSmartList('Unit', 2);
        })


        var arr = [];
        var $select = $('.s');
        var index = 0;
        for (var i = index; i < $select.length; i++) {
            $select.eq(i).get(0).index = i;
        }

        function se(dom, index) {
            dom.eq(index++).on('change', function () {
                for (var i = index; i < dom.length; i++) {
                    dom.eq(i).html('<option value="" disabled selected>请选择</option>');
                }
                arr[$(this).get(0).index] = $(this).val();
                dom.eq(index).html($(this).html()).find('option').each(function () {
                    var l = $(this).parent().get(0).index;
                    for (var i = 0; i < l; i++) {
                        if ($(this).val() === arr[i]) {
                            $(this).hide();
                        }
                    }
                })
            });
            if (index < dom.length) {
                se(dom, index);
            }
        }

        se($select, 0);


        $("#hSearch").on("click", function () {
            parent.$("#zzsModal").modal('hide');
            parent.accessSearch($('#fm').serialize());
        });

    });
</script>
<script src="../js/zzsCommon.min.js"></script>
</html>

```

