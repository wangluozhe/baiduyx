# 序言
昨天我在微信群里面看了一个很有趣的网站，就是输入自己想要输入的问题，然后再帮你生成一个链接，你访问后就自动给你跳转到相应的百度链接上去，这个网站实现并不是很难，只是很有趣而已，所以在这里分享一个自己纯手写的，原生js写出来的“让我帮你百度一下”这个站点，先看看效果图吧！

![效果图](https://img-blog.csdnimg.cn/2019080110251865.gif)
上面就是这个网站的效果图，怎么样简单吧。
# HTML页面
```html
<!DOCTYPE html>
<!--
    作者：LeeGene
-->
<html>
    <head>
        <meta charset="UTF-8" />
        <title>让我帮你百度一下</title>
    </head>
    <body>
	<img id="arrow" src="img/arrow.png" alt="arrow">
	<span class="about">?</span>
	<div class="container">
		<h1 class="title">需要我帮你<img class="baidu" src="img/baidu_logo.png" alt="百度">么</h1>
		<span class="search-container">
			<input type="text" id="search" />
			<input type="button" id="get" class="button" value="百度一下" />
		</span>
		<div id="instrcutions">输入一个问题，然后按百度一下</div>
		<div id="link">
			<input type="text" readonly="readonly" id="url" />
			<div>
				<input style="margin: 0 10px;" type="button" class="button" id="copy" value="复制" />
				<input style="margin: 0 10px;" type="button" class="button" id="skip" value="预览" />
			</div>
		</div>
	</div>
    </body>
</html>
```
页面没有CSS样式：
![效果图](https://img-blog.csdnimg.cn/20190801104821824.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjc5MDc3,size_16,color_FFFFFF,t_70)
这个样子是不是很丑，给他加上css样式。

# CSS样式
```html
<style type="text/css">
	*{
		margin: 0;
		padding: 0;
		box-sizing: border-box;
		-webkit-box-sizing: border-box;
		-moz-box-sizing: border-box;
	}
	html,body{
		width: 100%;
		height: 100%;
	}
	img{
		font-size: 0;
	}
	#arrow{
		display: none;
		position: absolute;
		top: 0;
		left: 0;
		transition: transform 2s ease-in-out;
		-webkit-transition: transform 2s ease-in-out;
		-moz-transition: transform 2s ease-in-out;
	}
	.about{
		position: absolute;
		width: 25px;
		height: 25px;
		display: inline-block;
		text-align: center;
		line-height: 25px;
		background-color: rgba(0,0,0,.5);
		border-radius: 50%;
		float: right;
		color: white;
		top: 15px;
		right: 15px;
		cursor: pointer;
	}
	.container{
		height: 500px;
		margin: auto;
		text-align: center;
	}
	.title{
		text-align: center;
		padding-top: 100px;
		margin-bottom: 20px;
	}
	.baidu{
		height: 100px;
		margin-bottom: -20px;
	}
	#search{
		width: 537px;
		height: 40px;
		padding: 9px 7px;
		outline: 0;
		border: 1px solid #d8d8d8;
		vertical-align: top;
		font-size: 16px;
	}
	.button{
		width: 102px;
		height: 40px;
		background-color: #38f;
		border: 0;
		padding: 0;
		color: white;
		cursor: pointer;
		margin-left: -10px;
	}
	#instrcutions{
		color: #999;
		margin-top: 70px;
		font-size: 1.1em;
		margin-bottom: 40px;
	}
	#url{
		width: 300px;
		height: 40px;
		margin-bottom: 20px;
		outline: 0;
		border: 1px solid #D8D8D8;
		font-size: 20px;
	}
	#link{
		display: none;
	}
	@media screen and (max-width:668px) {
		.container{
			padding: 15px;
		}
		.title{
			font-size: 1em;
			font-weight: normal;
		}
		.baidu{
			height: 50px;
			margin-bottom: -8px;
		}
		.search-container{
			width: 100%;
		}
		#search{
			width: calc(100% - 102px);
		}
	}
</style>
```
页面带有CSS样式：
![效果图](https://img-blog.csdnimg.cn/20190801105057875.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjc5MDc3,size_16,color_FFFFFF,t_70)
是不是效果完全不一样了，但是仅仅有这两个还不行，还得有鼠标移动动画以及文字一一输出动画和跳转链接动画，所以还得依靠js来实现这几个功能，这样就能完成咱们的这个项目了。

# JS实现功能
```html
<script type="text/javascript">
	// 获取链接并显示在网页上
	function getLink(){
		document.getElementById('link').style.display = "block";
		var value = document.getElementById('search').value;
		var base_url = location.href;
		url = base_url + "?" + value;
		document.getElementById('url').value = url;
		document.getElementById('url').select();
		document.getElementById('instrcutions').innerText = "复制下面的地址,然后发给伸手党吧！";
	}
	// 将链接自动拷贝到剪切板中
	function copyLink(){
		document.execCommand('copy');
	}
	// 跳转网页到所生成的链接上
	function skipLink(){
		location.href = url;
	}
	// 获取网页顶部get传递的参数,如:http://127.0.0.1/?123,问号后面的123就是get传递的参数
	function getVariable(){
		var link = location.href;
		var variable = link.split('?')[1];
		return variable;
	}
	// 自动执行
	(function(window){
		var variable = getVariable();	// 获取网页链接的get参数
		// 实现点击"百度一下"按钮就获取链接并显示在网页上
		document.getElementById('get').addEventListener('click',function(){
			getLink();
		});
		// 实现点击"复制"按钮就将生成的链接复制到剪切板上
		document.getElementById('copy').addEventListener('click',function(){
			copyLink();
		});
		// 实现点击"预览"按钮就跳转到生成的链接上
		document.getElementById('skip').addEventListener('click',function(){
			skipLink();
		});
		// 实现点击"?"文本就弹出提示声明
		document.getElementsByClassName('about')[0].addEventListener('click',function(){
			alert('*本站与百度公司没有任何联系，baidu以及本站出现的百度公司Logo是百度公司的商标。');
		});
		if(variable){	// 判断有没有get传递的参数,有就进入动画效果,没有就保持不变
			var arrow = document.getElementById('arrow');
			var search = document.getElementById('search');
			var instrcutions = document.getElementById('instrcutions');
			var search_offsetX = search.offsetLeft + 8;	// 获取搜索框的X轴位置
			var search_offsetY = search.offsetTop + 10;	// 获取搜索框的Y轴位置
			arrow.style.display = "block";
			instrcutions.innerText = "1、找到输入框并选中";	// 鼠标进行第一层动画的提示语
			setTimeout(function(){	// 鼠标进行第一层动画
				arrow.style.transform = "translate(" + search_offsetX + "px," + search_offsetY + "px)";
			},0);
			(function(){
				setTimeout(function(){	// 鼠标进行第二层动画
					var variable = decodeURIComponent(getVariable());
					var index = 0;	// 给与一个下标号
					arrow.style.display = "none";
					instrcutions.innerText = "2、输入你的问题";	// 鼠标进行第二层动画的提示语
					var a = setInterval(function(){	// 文字一一输入的特效
						if(index == variable.length){	// 判断输出字符串是否到达极限,并且鼠标进行第三层动画
							clearInterval(a);	// 到达极限删除自身,避免死循环浪费内存
							instrcutions.innerText = "3、按下“百度一下”按钮";	// 鼠标进行第三层动画的提示语
							arrow.style.display = "block";
							var get = document.getElementById('get');
							var get_offsetX = get.offsetLeft + 51;	//获取"百度一下"按钮的X轴位置
							var get_offsetY = get.offsetTop + 20;	//获取"百度一下"按钮的Y轴位置
							arrow.style.transform = "translate("+ get_offsetX +"px," + get_offsetY + "px)";
							setTimeout(function(){
								instrcutions.innerText = "这对你而言就是这么困难么？";		// 第三层动画结束语
							},2000);
							setTimeout(function(){
								location.href = "https://www.baidu.com/s?ch=3&ie=utf-8&wd=" + variable;	// 自动跳转到百度搜索界面
							},3000);
						}
						search.value = variable.substr(0,index++);	// 没达到极限继续输出字符串
					},200);
				},2000);
			})();
		}
	})(window);
</script>
```
页面带有JS功能：
![效果图](https://img-blog.csdnimg.cn/2019080110251865.gif)
整个结构就是这个样子，js文件中有解释，所以这里就不一一解释了
# 完整页面源码
```html
<!DOCTYPE html>
<!--
    作者：LeeGene
-->
<html>
	<head>
		<meta charset="utf-8" />
		<title>让我帮你百度一下</title>
		<style type="text/css">
			*{
				margin: 0;
				padding: 0;
				box-sizing: border-box;
				-webkit-box-sizing: border-box;
				-moz-box-sizing: border-box;
			}
			html,body{
				width: 100%;
				height: 100%;
			}
			img{
				font-size: 0;
			}
			#arrow{
				display: none;
				position: absolute;
				top: 0;
				left: 0;
				transition: transform 2s ease-in-out;
				-webkit-transition: transform 2s ease-in-out;
				-moz-transition: transform 2s ease-in-out;
			}
			.about{
				position: absolute;
				width: 25px;
				height: 25px;
				display: inline-block;
				text-align: center;
				line-height: 25px;
				background-color: rgba(0,0,0,.5);
				border-radius: 50%;
				float: right;
				color: white;
				top: 15px;
				right: 15px;
				cursor: pointer;
			}
			.container{
				height: 500px;
				margin: auto;
				text-align: center;
			}
			.title{
				text-align: center;
				padding-top: 100px;
				margin-bottom: 20px;
			}
			.baidu{
				height: 100px;
				margin-bottom: -20px;
			}
			#search{
				width: 537px;
				height: 40px;
				padding: 9px 7px;
				outline: 0;
				border: 1px solid #d8d8d8;
				vertical-align: top;
				font-size: 16px;
			}
			.button{
				width: 102px;
				height: 40px;
				background-color: #38f;
				border: 0;
				padding: 0;
				color: white;
				cursor: pointer;
				margin-left: -10px;
			}
			#instrcutions{
				color: #999;
				margin-top: 70px;
				font-size: 1.1em;
				margin-bottom: 40px;
			}
			#url{
				width: 300px;
				height: 40px;
				margin-bottom: 20px;
				outline: 0;
				border: 1px solid #D8D8D8;
				font-size: 20px;
			}
			#link{
				display: none;
			}
			@media screen and (max-width:668px) {
				.container{
					padding: 15px;
				}
				.title{
					font-size: 1em;
					font-weight: normal;
				}
				.baidu{
					height: 50px;
					margin-bottom: -8px;
				}
				.search-container{
					width: 100%;
				}
				#search{
					width: calc(100% - 102px);
				}
			}
		</style>
	</head>
	<body>
		<img id="arrow" src="img/arrow.png" alt="arrow">
		<span class="about">?</span>
		<div class="container">
			<h1 class="title">需要我帮你<img class="baidu" src="img/baidu_logo.png" alt="百度">么</h1>
			<span class="search-container">
				<input type="text" id="search" />
				<input type="button" id="get" class="button" value="百度一下" />
			</span>
			<div id="instrcutions">输入一个问题，然后按百度一下</div>
			<div id="link">
				<input type="text" readonly="readonly" id="url" />
				<div>
					<input style="margin: 0 10px;" type="button" class="button" id="copy" value="复制" />
					<input style="margin: 0 10px;" type="button" class="button" id="skip" value="预览" />
				</div>
			</div>
		</div>
		<script type="text/javascript">
			// 获取链接并显示在网页上
			function getLink(){
				document.getElementById('link').style.display = "block";
				var value = document.getElementById('search').value;
				var base_url = location.href;
				url = base_url + "?" + value;
				document.getElementById('url').value = url;
				document.getElementById('url').select();
				document.getElementById('instrcutions').innerText = "复制下面的地址,然后发给伸手党吧！";
			}
			// 将链接自动拷贝到剪切板中
			function copyLink(){
				document.execCommand('copy');
			}
			// 跳转网页到所生成的链接上
			function skipLink(){
				location.href = url;
			}
			// 获取网页顶部get传递的参数,如:http://127.0.0.1/?123,问号后面的123就是get传递的参数
			function getVariable(){
				var link = location.href;
				var variable = link.split('?')[1];
				return variable;
			}
			// 自动执行
			(function(window){
				var variable = getVariable();	// 获取网页链接的get参数
				// 实现点击"百度一下"按钮就获取链接并显示在网页上
				document.getElementById('get').addEventListener('click',function(){
					getLink();
				});
				// 实现点击"复制"按钮就将生成的链接复制到剪切板上
				document.getElementById('copy').addEventListener('click',function(){
					copyLink();
				});
				// 实现点击"预览"按钮就跳转到生成的链接上
				document.getElementById('skip').addEventListener('click',function(){
					skipLink();
				});
				// 实现点击"?"文本就弹出提示声明
				document.getElementsByClassName('about')[0].addEventListener('click',function(){
					alert('*本站与百度公司没有任何联系，baidu以及本站出现的百度公司Logo是百度公司的商标。');
				});
				if(variable){	// 判断有没有get传递的参数,有就进入动画效果,没有就保持不变
					var arrow = document.getElementById('arrow');
					var search = document.getElementById('search');
					var instrcutions = document.getElementById('instrcutions');
					var search_offsetX = search.offsetLeft + 8;	// 获取搜索框的X轴位置
					var search_offsetY = search.offsetTop + 10;	// 获取搜索框的Y轴位置
					arrow.style.display = "block";
					instrcutions.innerText = "1、找到输入框并选中";	// 鼠标进行第一层动画的提示语
					setTimeout(function(){	// 鼠标进行第一层动画
						arrow.style.transform = "translate(" + search_offsetX + "px," + search_offsetY + "px)";
					},0);
					(function(){
						setTimeout(function(){	// 鼠标进行第二层动画
							var variable = decodeURIComponent(getVariable());
							var index = 0;	// 给与一个下标号
							arrow.style.display = "none";
							instrcutions.innerText = "2、输入你的问题";	// 鼠标进行第二层动画的提示语
							var a = setInterval(function(){	// 文字一一输入的特效
								if(index == variable.length){	// 判断输出字符串是否到达极限,并且鼠标进行第三层动画
									clearInterval(a);	// 到达极限删除自身,避免死循环浪费内存
									instrcutions.innerText = "3、按下“百度一下”按钮";	// 鼠标进行第三层动画的提示语
									arrow.style.display = "block";
									var get = document.getElementById('get');
									var get_offsetX = get.offsetLeft + 51;	//获取"百度一下"按钮的X轴位置
									var get_offsetY = get.offsetTop + 20;	//获取"百度一下"按钮的Y轴位置
									arrow.style.transform = "translate("+ get_offsetX +"px," + get_offsetY + "px)";
									setTimeout(function(){
										instrcutions.innerText = "这对你而言就是这么困难么？";		// 第三层动画结束语
									},2000);
									setTimeout(function(){
										location.href = "https://www.baidu.com/s?ch=3&ie=utf-8&wd=" + variable;	// 自动跳转到百度搜索界面
									},3000);
								}
								search.value = variable.substr(0,index++);	// 没达到极限继续输出字符串
							},200);
						},2000);
					})();
				}
			})(window);
		</script>
	</body>
</html>
```
以上就是整个项目的具体介绍了，源代码可以随意使用和更改，但请保留作者著作。
