# 闭包
```
function f1(){  
　　　var n=999;  
    //闭包  
　　　nAdd=function(){n+=1}  
    //闭包  
　　　function f2(){  
　　　　　alert(n);  
　　　}  
　　　return f2;  
　}  
　var result=f1();  
　result(); // 999，函数f1外部访问f1内部的局部变量  

　nAdd();  
　result(); // 1000 
```
例子：
```
var name = "The Window";  
var object = {  
　　name : "My Object",  
　　getNameFunc : function(){  
　　　　return function(){  
　　　　　　return this.name;  
　　　　};  
　　}  
};  
alert(object.getNameFunc()());//The Window  
  
var name = "The Window";  
var object = {  
　　name : "My Object",  
　　getNameFunc : function(){  
　　　　var that = this;  
　　　　return function(){  
　　　　　　return that.name;  
　　　　};  
　　}  
};  
alert(object.getNameFunc()());//My Object  
```
