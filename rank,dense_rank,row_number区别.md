rank,dense_rank,row_number区别

一：语法(用法)：
     rank() over([partition by col1] order by col2) 
     dense_rank() over([partition by col1] order by col2) 
     row_number() over([partition by col1] order by col2) 
     其中[partition by col1]可省略。

二：区别
    三个分析函数都是按照col1分组内从1开始排序
    

    row_number() 是没有重复值的排序(即使两天记录相等也是不重复的)，可以利用它来实现分页
    dense_rank() 是连续排序，两个第二名仍然跟着第三名
    rank()       是跳跃拍学，两个第二名下来就是第四名
