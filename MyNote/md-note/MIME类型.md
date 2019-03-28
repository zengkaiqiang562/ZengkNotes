## MIME类型 ##
互联网媒体类型（Internet media type），也称为 MIME类型（MIME type）或 内容类型（content type）。
是给互联网上传输的内容赋予的分类类型。

一份内容的互联网媒体类型是由其文件格式与内容决定的。
互联网媒体类型与文件拓展名相对应，因此计算机系统常常通过拓展名来确定一个文件的媒体类型，
并决定与其相关联的软件。

**MIME类型的组成**
 一个MIME类型至少包括两个部分：一个类型（type）和一个子类型（subtype）。
此外，它还可能包括一个或多个可选参数（optional parameter）。
通用写法： `类型名/子类型名[; 可选参数] `

MIME类型对大小写不敏感，但是传统写法都是小写

type 表示可以被分多个子类的独立类别。subtype 表示细分后的每个类型。

举例：

	text/html; charset=UTF-8

在这个例子中，文件类型为text，子类型为html，而charset是一个可选参数，其值为UTF-8。 

当前已被注册的类型名有：

	application

	audio

	example

	image

	message

	model

	multipart

	text

	video

对于text文件类型若没有特定的subtype，就使用 text/plain。
类似的，二进制文件没有特定或已知的 subtype，即使用 application/octet-stream。

## 常用的MIME类型 与 对应的文件拓展名 ##

### Type application ###
表明是某种二进制数据

**application/ecmascript**  
相当于application/javascript但是严格的处理规则

**application/javascript**  
相当于application/ecmascript但是宽松的处理规则
JavaScript (ECMAScript)
.js

**application/json**
JSON format
.json

**application/octet-stream**  
任意的二进制文件（通常做为通知浏览器下载文件）。
所有其他情况的默认值。一种未知的文件类型应当使用此类型。

Any kind of binary data
.bin

**application/ogg**  
Ogg, 视频文件格式
OGG
.ogx

**application/pdf**
Adobe Portable Document Format (PDF)
.pdf

**application/xhtml+xml**  
XHTML
.xhtml

**application/xml**  
XML文件
.xml

**application/xml-dtd**  
DTD文件

**application/msword**
Microsoft Word
.doc

**application/vnd.ms-powerpoint**
Microsoft PowerPoint
.ppt

**application/vnd.ms-excel**
Microsoft Excel
.xls

**application/vnd.openxmlformats-officedocument.spreadsheetml.sheet**
Microsoft Excel (OpenXML)
.xlsx

**application/epub+zip**
Electronic publication (EPUB)
.epub

**application/zip**  
ZIP压缩档
.zip

**application/gzip**  
Gzip

**application/x-bzip**
BZip archive
.bz

**application/x-bzip2**
BZip2 archive
.bz2

**application/java-archive**
Java Archive (JAR)
.jar

**application/x-rar-compressed**
RAR archive
.rar

**application/x-tar**
Tape Archive (TAR)
.tar

**application/x-7z-compressed**
7-zip archive
.7z

**application/vnd.oasis.opendocument.text**
OpenDocument text document
.odt

**application/rtf**
Rich Text Format (RTF)
.rtf

**application/x-sh**
Bourne shell script
.sh

**application/x-font-ttf**
TrueType Font
.ttf

**application/x-font-woff**
Web Open Font Format (WOFF)
.woff

**application/vnd.visio**
Microsft Visio
.vsd

### Type audio ###
数字音频文件

**audio/mp4**  
MP4音频档案

**audio/mpeg ** 
MP3或其他MPEG音频档案

**audio/ogg**  
Ogg音频档案
OGG audio
.oga

**audio/vnd.wave** 
WAV音频档案

**audio/x-wav**
Waveform Audio Forma
.wav

**audio/vnd.rn-realaudio**  
RealAudio音频档案

**audio/x-flac**  
FLAC音频档案

**audio/aac ** 
AAC audio file
.aac

**audio/3gpp**
3GPP audio/video container, but it doesn't contain video
.3gp

### Type image ###
表明是某种图像。不包括视频，但是动态图（比如动态gif）也使用image类型

**image/gif**  
GIF图像文件
Graphics Interchange Format (GIF)
.gif

**image/jpeg**  
JPEG图像文件
.jpeg
.jpg

**image/png**  
PNG图像文件

**image/svg+xml**  
SVG向量图像文件
Scalable Vector Graphics (SVG)
.svg

**image/tiff**  
TIFF图像文件
Tagged Image File Format (TIFF)
.tif
.tiff

### Type message ###

### Type model ###
三维计算机图形文件

### Type multipart ###
Multipart 类型表示细分领域的文件类型的种类，经常对应不同的 MIME 类型。
这是复合文件的一种表现方式。

#### multipart/form-data ####
在HTML中，通过HTTP协议的POST方法上传包含多种类型文件的Form表单时，使用此媒体类型。表示上传的数据
包含多种类型的文件。如可能包含文本文件（text/plain），和图片文件（image/png）

multipart/form-data 作为多部分文档格式，它由边界线（一个由'--'开始的字符串）划分出的不同部分组成。
每一部分表示一个表单域，每个表单域都有自己的内容实体，以及自己的 HTTP 实体首部字段。

通常，每个表单域都一个Content-Disposition请求头字段。
特别地，对于文件上传域，除了有Content-Disposition字段外，还包含有Content-Type字段，此字段用于表示
上传文件的MIME类型。

表单域中没有Content-Length字段（只有请求首部字段中才有Content-Length），这是因为边界线作为分隔符
不能算作内容长度。

multipart/form-data 类型的文档格式如下：

	Content-Type: multipart/form-data; boundary=aBoundaryString
	(other headers associated with the multipart document as a whole)
	
	--aBoundaryString
	Content-Disposition: form-data; name="myFile"; filename="img.jpg"
	Content-Type: image/jpeg
	
	(data)
	--aBoundaryString
	Content-Disposition: form-data; name="myField"
	
	(data)
	--aBoundaryString
	(more subparts)
	--aBoundaryString--

举例：
	
	HTML中的表单如下：

	<form action="http://localhost:8000/" method="post" enctype="multipart/form-data">
	  <input type="text" name="myTextField">
	  <input type="checkbox" name="myCheckBox">Check</input>
	  <input type="file" name="myFile">
	  <button>Send the file</button>
	</form>

	--------
	上传上面的表单时，HTTP请求如下:

	POST / HTTP/1.1
	Host: localhost:8000
	User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
	Accept-Language: en-US,en;q=0.5
	Accept-Encoding: gzip, deflate
	Connection: keep-alive
	Upgrade-Insecure-Requests: 1
	Content-Type: multipart/form-data; boundary=---------------------------8721656041911415653955004498
	Content-Length: 465
	
	-----------------------------8721656041911415653955004498
	Content-Disposition: form-data; name="myTextField"
	
	Test
	-----------------------------8721656041911415653955004498
	Content-Disposition: form-data; name="myCheckBox"
	
	on
	-----------------------------8721656041911415653955004498
	Content-Disposition: form-data; name="myFile"; filename="test.txt"
	Content-Type: text/plain
	
	Simple file.
	-----------------------------8721656041911415653955004498--

如上， text.txt文件的内容就是 Simple file.

#### multipart/byteranges ####
表示实体中的内容只是某文件中的部分内容。
在断点下载中，服务器返回给客户端（或用户代理）的数据时，响应实体中的内容数据只是一份文件中的部分内容，
此时返回的响应状态码是 206（Partial Content）

multipart/byteranges 用于把部分的响应报文发送回浏览器。
当发送状态码206Partial Content 时，这个MIME类型用于指出这个文件由若干部分组成，每一个都有其请求范围。

就像其他很多类型Content-Type使用分隔符来制定分界线。
每一个不同的部分都有Content-Type这样的HTTP头来说明文件的实际类型，以及 Content-Range来说明其范围。

	HTTP/1.1 206 Partial Content
	Accept-Ranges: bytes
	Content-Type: multipart/byteranges; boundary=3d6b6a416f9b5
	Content-Length: 385
	
	--3d6b6a416f9b5
	Content-Type: text/html
	Content-Range: bytes 100-200/1270
	
	eta http-equiv="Content-type" content="text/html; charset=utf-8" />
	    <meta name="vieport" content
	--3d6b6a416f9b5
	Content-Type: text/html
	Content-Range: bytes 300-400/1270
	
	-color: #f0f0f2;
	        margin: 0;
	        padding: 0;
	        font-family: "Open Sans", "Helvetica
	--3d6b6a416f9b5--

### Type text ###
表明文件是普通文本，理论上是人类可读

**text/css**  
CSS文件
Cascading Style Sheets (CSS)
.css

**text/csv ** 
CSV文件
Comma-separated values (CSV)
.csv

**text/html**  
HTML文件
HyperText Markup Language (HTML)
.htm
.html

所有的HTML内容都应该使用这种类型。
XHTML的其他MIME类型（如application/xml+html）现在基本不再使用（HTML5统一了这些格式）。

**text/javascript**  
过时。推荐使用 application/javascript。

但是，相比于 application/javascript ，在 HTML 4 和 5 中，可以使用text/javascript ，
且有跨浏览器的支持。

**text/plain**
文本文件默认值。即使它意味着未知的文本文件，但浏览器认为是可以直接展示的。

**text/xml**  
XML

**text/vcard**  
vCard（电子名片）

### Type video ###
视频文件格式文件（可能包含数字视频与数字音频）

**video/mpeg ** 
MPEG-1视频文件
MPEG Video
.mpeg

**video/mp4**  
MP4视频文件

**video/ogg**  
Ogg视频文件
OGG video
.ogv

**video/x-flv ** 
Flash Video（FLV档）

**video/x-ms-wmv  **
Windows Media Video视频文件

**video/x-msvideo**
AVI: Audio Video Interleave 	
.avi 	

**video/3gpp**
3GPP audio/video container
.3gp