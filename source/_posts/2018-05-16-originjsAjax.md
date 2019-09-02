---

title: 原生js实现Ajax
date: 2018-05-16 19:41:32
tags: js
categories: 
	- javaScript相关
---

原生js实现Ajax
=============

```javascript
function ajax(){
var ajaxData = {
    type:arguments[0].type || 'GET',
    url：arguments[0].url || '',
    aysnc:arguments[0].async || 'true',
    data:arguments[0].data || null,
    dataType:arguments[0].dataType || 'text',
    contentType:arguments[0].contentType || 'application/x-www-form-urlencoded',
    beforeSend:arguments[0].beforeSend || function(){},
    success:arguments[0].success || function(){},
    error:arguments[0].error || function(){},
}
ajaxData.beforeSend(){
    var xhr = createxmlHttpRequest();
    xhr.responseType = ajaxData.dataType;
    xhr.open(ajaxData.type,ajaxData.url,ajaxData.async);
    xhr.setRequestHeader("Content-Type",ajaxData.contentType);  
    xhr.send(convertData(ajaxData.data));  
    xhr.onreadystatechange = function() {  
        if (xhr.readyState == 4) {  
            if(xhr.status == 200){ 
                ajaxData.success(xhr.response) 
            }else{ 
                ajaxData.error() 
            }  
        } 
    }
}
function createxmlHttpRequest() {  
    if (window.ActiveXObject) {  
        return new ActiveXObject("Microsoft.XMLHTTP");  
    } else if (window.XMLHttpRequest) {  
        return new XMLHttpRequest();  
    }  
} 
function convertData(data){ 
    if( typeof data === 'object' ){ 
        var convertResult = "" ;  
        for(var c in data){  
            convertResult+= c + "=" + data[c] + "&";  
        }  
        convertResult=convertResult.substring(0,convertResult.length-1) 
        return convertResult; 
    }else{ 
        return data; 
    } 
}
}

// 调用
ajax({ 
    type:"POST", 
    url:"ajax.php", 
    dataType:"json", 
    data:{"val1":"abc","val2":123,"val3":"456"}, 
    beforeSend:function(){ 
    //some js code 
    }, 
    success:function(msg){ 
    console.log(msg) 
    }, 
    error:function(){ 
    console.log("error") 
    } 
})
```