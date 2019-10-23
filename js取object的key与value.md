var obj = {"name1":"张三","name2":"李四"};

1、js中想根据动态key得到某对象中相对应的value的方法有二：

一、var key = "name1";var value = obj[key]; 
二、var key = "name1";var value = eval("obj."+key);


2、

   for(var p in obj){                   

                  console.log(p);//   取得是key值
                  console.log(obj[p]);//取得是value值
    }