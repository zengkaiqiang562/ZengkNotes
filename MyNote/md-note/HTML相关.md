## 表单 ##

	<form id="register" method="post" action="jsp文件路径|Servlet的urlPattern">
		用户名：<br/>
	   <input type="text" name="username"/>
		性别：<br/>  
		男:  <input type="radio" name="gender" value="男"/>
		女： <input type="radio" name="gender" value="女"/>
		<hr/>
		喜欢的颜色：<br/>
		红： <input type="checkbos" name="color" value="红"/>
		绿： <input type="checkbos" name="color" value="绿"/>
		蓝： <input type="checkbos" name="color" value="蓝"/>	
		<hr/>	
		来自的国家：<br/>
		<select name="country">
			<option value="中国">中国</option>
			<option value="美国">美国</option>
			<option value="俄罗斯">俄罗斯</option>
		</select>
		<hr/>
		照片：<br/>
		选择文件： <input type="file" id="photofile" name="photofile"/>
		<hr/>
		<input type="submit" value="提交"/>
		<input type="reset" value="重置"/>
	</form>

并不是每个表单域都会生成请求参数，只有具有name属性的表单域才能生成请求参数。
表单域和请求参数之前有如下关系：

	每个有name属性的表单域对应一个请求参数
	如果有多个表单域有相同的name属性，则多个表单域只生成一个请求参数，只是该请求参数有多个值
	表单域的name属性指定请求参数名，value属性指定请求参数值
	如果某个表单域设置了disabled="disabled"，则该表单域不再生成请求参数。


可上传文件的表单：

	<form id="upload" action="upload" enctype="multipart/form-data">
		文件名：<br/>
	    <input type="text" name="username"/>
		<br/>
		选择文件： <input type="file" id="uploadfile" name="uploadfile"/>
		<hr/>
		<input type="submit" value="提交"/>
	</form>

表单中form标签需要指定enctype="multipart/form-data"，表示该表单可用于上传文件

表单的enctype属性指定表单数据的编码方式，该属性有如下三个值：

	application/x-www-form-urlencoded： 这是默认的编码方式，它只处理表单域里的value属性值，
		采用这个编码方式的表单会将表单域的值处理成URL编码方式。

	multipart/form-data： 这种编码方式会以二进制流的方式来处理表单数据，这个编码方式会把文件域指定	文件的内容也封装到请求参数中。

	text/plain： 这种编码方式当表单的action属性为mailto:URL的形式时比较方便，这种方式主要适用于直	接通过表单发送邮件的方式。

如果将enctype设置为application/x-www-form-urlencoded，或不设置enctype属性，那么提交表单时只会发送文件域的文本框中的字符串，也就是用户所选择的文件在客户端PC上的绝对路径。
