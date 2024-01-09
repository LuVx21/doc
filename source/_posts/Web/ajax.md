---
title: Java ajax
date: 2017-10-27
tags:
- Java
- Web
- Ajax
---
<!-- TOC -->

- [关于](#关于)
- [原生Ajax](#原生ajax)
    - [get请求](#get请求)
    - [post请求](#post请求)
- [jquery的ajax](#jquery的ajax)

<!-- /TOC -->

# 关于

AJAX 是一种用于创建快速动态网页的技术.
通过在后台与服务器进行少量数据交换, 能够实现无刷新状态更新页面和异步提交.这意味着可以在不重新加载整个网页的情况下, 对网页的某部分进行更新.
传统的网页(不使用 AJAX)如果需要更新内容, 必须重载整个网页页面.

优点:
a.改善用户体验,可以在不更新整个网页的情况下,更新其中的一部分数据,交互性体验较好;
b.减少带宽的使用并增加速度,ajax可以按需取数据,而不需要重新载入整个网页,因此可以节省服务器开销和提高接口的响应速度;
c.支持异步处理,可以将事件进行分割,通过不同的请求来完成;

缺点:
a.移动设备的兼容性,由于ajax对javascript的依赖性,所以不适用于移动应用;
b.不安全性,ajax增加了网页逻辑结构的复杂性,可能会有不可预料的安全威胁;
c.破坏了浏览器前进, 后退按钮

# 原生Ajax

1. 创建XmlHttpRequest对象
2. 注册状态变化的事件处理
3. 初始化xhr对象
4. 向服务器发送请求

```js
// 触发ajax请求的事件
document.getElementById("b1").onclick=function(){}
```

获取ajax引擎:非标准,各浏览器实现有所不同
```js
function createXmlHttpRequest(){
    var xmlHttp;
    //Firefox等
    try{
        xmlHttp = new XMLHttpRequest();
    }catch (e){
        //IE
        try{
            xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
        }catch (e){
            try{
                xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
            }catch (e){}
        }
    }
    return xmlHttp;
}
```

## get请求

```js
var xhr = createXmlHttpRequest();
xhr.onreadystatechange = function(){
    if(xhr.readyState == 4){
        if(xhr.status == 200||xhr.status == 304){
            var data = xhr.responseText;
            document.getElementById("d1").innerHTML = data;
        }
    }
}
xhr.open("GET","/ajax/servlet/index1?time=" + new Date().getTime());
xhr.send(null);
```

## post请求

```js
var xhr = createXmlHttpRequest();
xhr.onreadystatechange = function(){
    if (xhr.readyState == 4) {
        if (xhr.status == 200 || xhr.status == 304) {
        }
    }
}
xhr.open("POST", "/ajax/servlet/index2?time=" + new Date().getTime());
xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
xhr.send("username=luvx&password=123");
}
```



ajax-api详解
    常用属性:
        onreadystatechange:检测readyState状态改变的时候
        readyState:ajax核心对象的状态
            0:核心对象创建
            1:调用了open方法
            2:调用了send方法
            3:部分响应已经生成(没有意思)
            4:响应已经完成(使用的是这个状态)
        status:状态码
            if(xmlhttp.readyState==4 && xmlhttp.status==200){
            }
        responseText:响应回来的文本
    常用方法:
        open("请求方式", "请求路径"[, "是否异步"]):设置请求的方式和请求的路径
        send(["参数"]):发送请求 参数是请求方式为post的时候的参数
        setRequestHeader("content-type", "form表单enctype属性"):设置post请求的参数的类型 必须放在send方法之前.



步骤分析:
    1.数据库和表
        CREATE TABLE `user` (
          `id` INT(11) NOT NULL AUTO_INCREMENT,
          `username` VARCHAR(20) DEFAULT NULL,
          `password` VARCHAR(20) DEFAULT NULL,
          `email` VARCHAR(20) DEFAULT NULL,
          `name` VARCHAR(20) DEFAULT NULL,
          `sex` VARCHAR(10) DEFAULT NULL,
          `birthday` DATE DEFAULT NULL,
          `hobby` VARCHAR(50) DEFAULT NULL,
          PRIMARY KEY (`id`)
        ) ENGINE=INNODB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;
        INSERT
        INTO `user`(`id`, `username`, `password`, `email`, `name`, `sex`, `birthday`, `hobby`)
        VALUES
        (1, 'bbb', '123', '123@163.com', '张三', '男', '1992-01-02', '篮球, 足球, 排球'),
        (2, 'ccc', '123', 'ccc@itcast.cn', '李四', '女', '1992-03-20', '排球, 乒乓球'),
        (3, 'aaa', '123', 'aaa@itcast.cn', '王守义', '男', '1990-08-11', '足球, 排球'),
        (5, 'tom', '123', 'haha@qq.com', '提莫', '男', NULL, '篮球');
    2.新建项目
        导入 jar包 工具类 配置文件
    3.新建一个注册页面 表单 在用户名文本框上输入用户名 失去焦点
        发送ajax请求, 将输入的值发送到servlet
    4.checkUsername4Ajax
        接受用户名
        调用service完成查询操作 返回一个用户
        判断user是否为空
            若为空 :写1 代表可以使用
            若不为空:写0
    5.在表单接受响应的数据
        判断一下,
            若为0, 则提示用户名已被占用 表单不可用提交 提交按钮禁用
                document.getElementById("sub").disabled=true;

# jquery的ajax

四种:
    了解:jquery对象.load(url, params, function(数据){});
    ★: $.get(url, params, function(数据){}, type);
        发送get请求的ajax
            url:请求的路径
            params:请求的参数 参数为key\value的形式 key=value  {"":"", "":""}
            fn:回调函数 参数就是服务器发送回来的数据
            type:返回内容格式, xml, html, script, json, text, _default.    以后用"json"

    ★: $.post(url, params, function(数据){}, type);
        发送post请求的ajax

        若结果为json格式, 打印返回值的时候是一个对象
            例如若json为 {"result":"success", "msg":"成功"}
            获取msg 只需要    参数.msg
    理解:
        $.ajax([选项]);
            选项的可选值:
                url:请求路径
                type:请求方法
                data:发送到服务器的数据
                success:fn 成功以后的回调
                error:fn 异常之后的回调
                dataType:返回内容格式 经常使用json
                async:设置是否是异步请求
            例如:
                $.ajax({
                    url:"/day15/demo1",
                    type:"post",
                    data:"username=tom",
                    success:function(d){
                        alert(d.msg);
                    },
                    error:function(){},
                    dataType:"json"

                });




案例3-模仿百度搜索
需求:
    在一个文本框中输入一段内容, keyup的时候发送一个ajax请求, 去数据库中查找相应的内容, 在页面上展示
步骤分析:
    1.表
        create table keyword(
            id int primary key auto_increment,
            kw varchar(20)
        );
    2.页面
    3.在文本框输入内容 keyup的时候 发送ajax请求 将输入值传递到后台
    4.将返回的数据展示
///////////////////////////////////////


案例4-省市联动
需求:
    先有一个省份的下拉选, 根据选择省份, 从而动态的市下拉选中加载所有的市.
步骤分析:
    1.表
    2.页面上有两个下拉选 省份的下拉选一般是固定的 页面加载的时候读取所有的省份
    3.当省份改变的时候, 获取省份的信息, 发送一个ajax请求, 去市的表中查询相应省份的所有市, 然后将他们加载到市下拉选上
    4.selectProServlet selectCityServlet
    //////////////////////
技术分析:
    json
        JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式.它基于ECMAScript的一个子集.
    json格式:
        格式1:value可以为任意值
            {"key":value, "key1":value1}
        格式2:e可以为任意值
            [e1, e2]
    jsonlib工具类, 可以使对象转换成json数据
        1.导入jar包
        2.使用api
            JSONArray.fromObject(对象)  数组和list
            JSONObject.fromObject(对象) bean和map

/////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////
原生ajax:
    1.获取核心对象 XMLHttpRequest
    2.编写回调函数
        xmlhttp.onreadystatechange=function(){
            if(xmlhttp.readyState==4 && xmlhttp.status==200){
                alert(xmlhttp.responseText);
            }
        }
    3.open操作 设置访问的方式和路径
        xmlhttp.open("get", "url");
    4.send操作
        xmlhttp.send("post的参数")
         若有参数需要设置一个头
            xmlhttp.setRequestHeader("content-type", "form表单enctype属性")
////////////////////////
jquery中ajax
    掌握的:
        $.get(url, params, fn, type);
            type:"json"
        $.post(url, params, fn, type);

    理解:
        $.ajax(选项);
            选项:
                url:
                type:
                data:
                success:fn
                error:
                dataType:"json"
json:轻量级的数据格式
    格式1:
        {"key":value, "key1":value}
    格式2:
        [e1, e2]
jsonlib:
    1.导入jar包
    2.使用api
        JSONArray.formObject(数组或者list);
        JSONObject.formObject(bean或者map);

//////////////////////////////////////////
获取jquery对象:
    $("选择器") jQuery("选择器");
jquery对象>>dom对象
    方式1:
        jquery对象.get(index);

    方式2:
        jquery对象[index]
dom对象>>jquery对象
    $(dom对象)

页面载入
    $(function(){})

派发事件
    jquery对象.事件(function(){...});

选择器:
    #id值  .class值  标签名  [属性]  [属性='值']
    a b:后代    a>b:孩子  a+b:大弟弟  a~b:所有弟弟
    :first :last :odd :even :eq|gt|lt(index)
    :hidden
    :checked  :selected
属性和css:
    prop|attr
    css

文本 标签体
    val()
    html() text()

文档处理
    内部插入
        append prepend
    外部插入
        after before
    删除
        remove
效果:
    隐藏|显示
        show() hide()
    淡入淡出
        fadeIn() fadeOut()
    滑入滑出
        slideDown() slideUp()

遍历
    jquery对象.each(function(){
    });

------------------------------
******四, AJAX原理
    4.1概述
    4.2编写步骤

    4.3XmlHttpRequest详解(JavaScript对象)
        常用属性:
            readyState:代表着XmlHttpRequest对象的当前状态
                0 (未初始化) 对象已建立,但是尚未初始化(尚未调用open方法)
                1 (初始化) 对象已建立,尚未调用send方法
                2 (发送数据) send方法已调用,但是当前的状态及http头未知
                3 (数据传送中) 已接收部分数据,因为响应及http头不全,
                4 (完成) 数据接收完毕,此时可以通过通过responseBody和responseText获取完整的回应数据
            只有为4,客户端操作相应的处理
            -------------------------------------------------
            status:代表服务器的HTTP相应码. 200是成功. 304服务器端内容没有改变.
            -------------------------------------------------
            responseText:服务器返回文本数据.

            onreadystatechange:当XmlHttpRequest对象的readyState发生变化时,都会触发该事件.

        常用方法:
            open(method,url,isAsync):初始化XmlHttpRequest对象.
                method:请求方式. 一般使用get或者post
                url:请求的服务器地址. 可以使用相对路径或者绝对路径.
                        特别注意:如果该地址没有变化,浏览器一般不会再次发出请求的. 解决办法,加上一个时间戳.
                            /ajaxday02/servlet/ServletDemo1?time="+new Date().getTime()
                isAsync:是否是异步请求. 默认是true.
            send(requestData):向服务器发送请求数据. 没有传递null.
                数据时用在POST请求方式的. 数据形式:username=admin&password=123

        通过XmlHttpRequest向服务器发送POST请求:
            //设置请求消息头,告知服务器,发送的正文数据的类型.
                xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");//固定写法
                //发送数据
                xhr.send("username=admin&password=123");
    4.4服务端返回的数据
        HTML数据
            responseText:他是XmlHttpRequest对象的一个属性. 服务器返回的数据会封装到此属性中.

        XML数据
            responseXML:返回的是xml对象的DOM对象.

        *****JSON数据
