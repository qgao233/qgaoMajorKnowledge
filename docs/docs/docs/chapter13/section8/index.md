# js的replace

首先是直接写正则表达式和new RegExp()里的正则表达式的不同之处，注释里写了。

其次是正则表达式的/g标志只针对replace时起作用，如果只单独执行exec只返回第1个，要想全部匹配，可以使用字符串的match方法：str.match(pattern)返回匹配到的数组。

最后是replace第2个可以为函数，而函数的参数为复数个，
* 第1个：每次匹配到的字符串，
* 第2个之后的复数个参数：正则中的分组()有多少个，就有多少个参数，
* 之后的1个参数：每次匹配到的字符串在字符串中的位置，
* 最后1个参数：原始字符串。

```
//将<font size=’2’>*</font>替换成<p style=’font-size:13px’>*</p>
export const transformFontTag = (html) => {  
    //new RegExp内空格不用\s，改为直接空格，\d要改为\\d  
    // const array = new RegExp("<font size=\"\\d*\">",'g').exec(htmlSource.html);  
    let pattern = /<font\ssize=\"(\d*)\">(.*?)<\/font>/g;//g这些标志位用在replace的时候  
    return htmlSource.html.replace(pattern,(val,p1,p2)=>{  
	// let str = val.match(/<font\ssize=\"\d*\">/g);  
	// let px = mapFontToPx[parseInt(str[0].split('\"')[1])];  
	// return val.replace(pattern,"<p style='font-size:"+px+"px'>$4</p>")  
	return "<p style='font-size:"+mapFontToPx[p1]+"px'>"+p2+"</p>"  
    })  
}  
```
