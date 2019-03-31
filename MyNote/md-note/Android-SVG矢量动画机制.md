## SVG 介绍 ##
SVG（Scalable Vector Graphics） 是指可伸缩的矢量图形。

Google在Android 5.X中增加了对SVG矢量图形的支持。
在Android 5.X 之前也可以通过一些第三方开源库在Android中使用SVG。
在Android 5.x 之后，可以通过`<path>`标签定义一份表示SVG图形的xml文件。

SVG矢量图形在放大或改变尺寸的情况下其图形质量不会有所损失（即不会失真）。所以在适配不同屏幕时，不需要
像Bitmap位图那样提供多套针对不同屏幕的位图图片。


## `<path>` 标签 ##
使用`<path>`标签创建SVG，就像用指令的方式来控制一只画笔

M = moveto(M X,Y) ：将画笔移动到指定的坐标位置

L = lineto(L X,Y) ：画直线到指定的坐标位置

H = horizontal lineto(H X)：画水平线到指定的X坐标位置

V = vertical lineto(V Y)：画垂直线到指定的Y坐标位置

C = curveto(C X1,Y1,X2,Y2,ENDX,ENDY)：三次贝赛曲线

S = smooth curveto(S X2,Y2,ENDX,ENDY)：三次贝赛曲线

Q = quadratic Belzier curve(Q X,Y,ENDX,ENDY)：二次贝赛曲线

T = smooth quadratic Belzier curveto(T ENDX,ENDY)：映射前面路径后的终点

A = elliptical Arc(A RX,RY,XROTATION,FLAG1,FLAG2,X,Y)：弧线

	RX,RY 指所在椭圆的半轴大小

	XROTATION 指椭圆的X轴与水平方向顺时针方向的夹角

	FLAG1 只有两个值，1表示大角度弧线，0为小角度弧线

	FLAG2 只有两个值，确定从起点至终点的方向，1为顺时针，0为逆时针

	X,Y 表示终点坐标

Z = closepath()：关闭路径


坐标轴为以(0,0)为中心，X轴水平向右，Y轴水平向下。
所有指令大小写均可。大写绝对定位，参照全局坐标系；小写相对定位，参照父容器坐标系
指令和数据间的空格可以省略
同一指令出现多次可以只用一个

## Android中使用 SVG ##
Google在Android 5.X中提供了两个新API来帮助支持SVG

    VectorDrawable
    AnimatedVectorDrawable

VectorDrawable让你可以创建基于XML的SVG图形，
并结合AnimatedVectorDrawable来实现动画效果

