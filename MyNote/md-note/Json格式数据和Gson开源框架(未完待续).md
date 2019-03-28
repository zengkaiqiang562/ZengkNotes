## Json


----
## Gson
gson参考博客：https://blog.csdn.net/qq_29078329/article/details/73331005

Gson是Google的一个Java开源库，利用序列化和反序列化技术，实现json字符串和Java对象间的转换。

导入Gson： 
dependencies{
	compile 'com.google.code.gson:gson:2.8.0'
}


**Gson实现反序列化**

JavaBean类的成员变量名要和json中的key一致，成员变量类型要和json中的value类型一致；并且要生成getter和setter方法。 

有时接口返回的字段命名和我们的编码风格不一致，如服务端返回{"thumbnail_pic_s": "http://04imgmini.eastday.com/}，为了继续使用驼峰标识，可以使用注解@SerializedName将驼峰变量跟返回字段绑定起来。

		@SerializedName("thumbnail_pic_s")  
		private String thumbnailUrl1;


Gson提供了@Expose注解来控制成员变量的序列化和非序列化，这个注解有两个变量：serialize和deserialize，默认都是true。若要使这个注解生效，必须使用GsonBuilder.excludeFieldsWithoutExposeAnnotation()方法来构建Gson对象。
Gson gson = new GsonBuilder().excludeFieldsWithoutExposeAnnotation().create();

Gson还提供了@Since注解进行版本控制，使用GsonBuilder构建时指定版本号，高于该版本的字段在序列化和反序列化时都将被忽略。
Gson gson = new GsonBuilder().setVersion(3.0).create();


举例

	{
	  "reason": "成功的返回",
	  "result": {
	    "stat": "1",
	    "data": [
	      {
	        "uniquekey": "bd696edc7c57728349e1591b88956fc1",
	        "title": "全视屏 6摄 荣耀magic2发布",
	        "date": "2018-11-02 23:32",
	        "category": "头条",
	        "author_name": "小曹科技说",
	        "url": "http://mini.eastday.com/mobile/181102233232223.html",
	        "thumbnail_pic_s": "http://04imgmini.eastday.com/mobile/20181102/20181102233232_f97bec6f8d0fdd7f3cb7db7a0d051c2d_1_mwpm_03200403.jpg",
	        "thumbnail_pic_s02": "http://04imgmini.eastday.com/mobile/20181102/20181102233232_f97bec6f8d0fdd7f3cb7db7a0d051c2d_2_mwpm_03200403.jpg",
	        "thumbnail_pic_s03": "http://04imgmini.eastday.com/mobile/20181102/20181102233232_f97bec6f8d0fdd7f3cb7db7a0d051c2d_5_mwpm_03200403.jpg"
	      },
	      {
	        "uniquekey": "4fd673398262fbe3d4a2b457f0b26791",
	        "title": "太平人寿9月重疾理赔数据出炉，对我们投保重疾险有哪些启示？",
	        "date": "2018-11-02 23:05",
	        "category": "头条",
	        "author_name": "成都保险经纪人谢鑫",
	        "url": "http://mini.eastday.com/mobile/181102230526374.html",
	        "thumbnail_pic_s": "http://03imgmini.eastday.com/mobile/20181102/20181102230526_0c23a442782787d64a934258ccacc760_1_mwpm_03200403.jpg",
	        "thumbnail_pic_s02": "http://03imgmini.eastday.com/mobile/20181102/20181102230526_0c23a442782787d64a934258ccacc760_2_mwpm_03200403.jpg"
	      },
	      {
	        "uniquekey": "62bbb5df7c9c9e8b9b08be25a593ef69",
	        "title": "中原银行成功举办“钻领金融，智赢理财”钻石理财师大赛",
	        "date": "2018-11-02 23:05",
	        "category": "头条",
	        "author_name": "河南财富圈",
	        "url": "http://mini.eastday.com/mobile/181102230518218.html",
	        "thumbnail_pic_s": "http://01imgmini.eastday.com/mobile/20181102/20181102230518_29d699e2a6818548ea6588daa4cd6412_1_mwpm_03200403.jpg",
	        "thumbnail_pic_s02": "http://01imgmini.eastday.com/mobile/20181102/20181102230518_29d699e2a6818548ea6588daa4cd6412_3_mwpm_03200403.jpg",
	        "thumbnail_pic_s03": "http://01imgmini.eastday.com/mobile/20181102/20181102230518_29d699e2a6818548ea6588daa4cd6412_2_mwpm_03200403.jpg"
	      }
	    ]
	  },
	  "error_code": 0
	}

	public class TopNewsModel {

		//{
		//"reason": "成功的返回",
		//"result": {TopNewsResult},
		//"error_code": 0
		//}

	    private String reason;
	    private TopNewsResult result;
	    @SerializedName("error_code")
	    private int errorCode;
	
	    public String getReason() {
	        return reason;
	    }
	
	    public TopNewsResult getResult() {
	        return result;
	    }
	
	    public int getErrorCode() {
	        return errorCode;
	    }
	
	    public void setReason(String reason) {
	        this.reason = reason;
	    }
	
	    public void setResult(TopNewsResult result) {
	        this.result = result;
	    }
	
	    public void setErrorCode(int errorCode) {
	        this.errorCode = errorCode;
	    }
	}

	public class TopNewsResult {
		//{
		//"stat": "1",
		//"data": [{TopNewsData},{TopNewsData},{TopNewsData}]
		//}

	    private String state;
	    private List<TopNewsData> data;
	
	    public String getState() {
	        return state;
	    }
	
	    public void setState(String state) {
	        this.state = state;
	    }
	
	    public List<TopNewsData> getData() {
	        return data;
	    }
	
	    public void setData(List<TopNewsData> data) {
	        this.data = data;
	    }
	}

	public class TopNewsData {
		//{
		//"uniquekey": "62bbb5df7c9c9e8b9b08be25a593ef69",
        //"title": "中原银行成功举办“钻领金融，智赢理财”钻石理财师大赛",
        //"date": "2018-11-02 23:05",
        //"category": "头条",
        //"author_name": "河南财富圈",
        //"url": "http://mini.eastday.com/mobile/181102230518218.html",
        //"thumbnail_pic_s": "http://01imgmini.eastday.com/mobile/20181102/20181102230518_29d699e2a6818548ea6588daa4cd6412_1_mwpm_03200403.jpg",
        //"thumbnail_pic_s02": "http://01imgmini.eastday.com/mobile/20181102/20181102230518_29d699e2a6818548ea6588daa4cd6412_3_mwpm_03200403.jpg",
        //"thumbnail_pic_s03": "http://01imgmini.eastday.com/mobile/20181102/20181102230518_29d699e2a6818548ea6588daa4cd6412_2_mwpm_03200403.jpg"
		//}		

	    @SerializedName("uniquekey")
	    private String uniqueKey;
	
	    private String title;
	
	    private String data;
	
	    private String category;
	
	    @SerializedName("author_name")
	    private String authorName;
	
	    private String url;
	
	    @SerializedName("thumbnail_pic_s")
	    private String thumbnailUrl1;
	
	    @SerializedName("thumbnail_pic_s02")
	    private String thumbnailUrl2;
	
	    @SerializedName("thumbnail_pic_s03")
	    private String thumbnailUrl3;
	
	    public String getUniqueKey() {
	        return uniqueKey;
	    }
	
	    ......
		//省略getter和setter方法
		......
	
	    @Override
	    public String toString() {
	        return "TopNewsData{" +
	                "uniqueKey='" + uniqueKey + '\'' +
	                ", title='" + title + '\'' +
	                ", data='" + data + '\'' +
	                ", category='" + category + '\'' +
	                ", authorName='" + authorName + '\'' +
	                ", url='" + url + '\'' +
	                ", thumbnailUrl1='" + thumbnailUrl1 + '\'' +
	                ", thumbnailUrl2='" + thumbnailUrl2 + '\'' +
	                ", thumbnailUrl3='" + thumbnailUrl3 + '\'' +
	                '}';
	    }
	}

----
##API
**Gson**
public Gson()
public <T> T fromJson(String json, Class<T> classOfT)