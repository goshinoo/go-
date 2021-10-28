# Beego笔记

## beego安装

```
go get github.com/astaxie/beego
```

## beego升级

```
go get -u github.com/astaxie/beego
```

## bee工具安装

```
go get github.com/beego/bee
```

## 创建项目

```
bee new 项目名
```

## 运行项目

```
// beego支持热更新,可以实时的看到项目修改之后的效果
bee run 项目名
```

## 项目结构

```go
project
├── conf
│   └── app.conf	//配置文件
├── controllers		
│   ├── admin
│   └── default.go	//控制器,可以有多个
├── main.go			//主入口
├── routers			
│   └── router.go	//路由
├── models
│	├── tools.go 	//可以存放模板函数
│   └── models.go	
├── static			//静态资源
│   ├── css
│   ├── ico
│   ├── img
│   └── js
└── views
    ├── admin
    └── index.tpl	//模板
```

## 配置静态资源目录:

```go
//默认为static目录
//可以有多个静态目录
beego.SetStaticPath("/down","download")
beego.SetStaticPath("/down2","download2")
或
//配置文件中
StaticDir = download:down download2:down2
```

## 参数配置:

```go
//获取
// mysqluser = "abc"
mysqluser := beego.AppConfig.String("mysqluser")

// admin = "abc","def","ll"
admin := beego.AppConfig.Strings("admin")
-----------------------------------
//全局设置
beego.AppConfig.Set("redisuser","admin")
-----------------------------------
//不同开发环境
[dev]
httpport = 8080
[prod]
httpport = 8088
[test]
httpport = 8888

//使用dev环境运行
//经测试无法使用=_=,改完mode无法生效端口
//bee run -runmode dev
//在配置文件中设置runmode
runmode = dev

//如需读取其他环境下的参数
beego.AppConfig.String("prod::httpport")
-----------------------------------
//加载其他配置文件
//ini是默认格式
beego.LoadAppConfig("ini","conf/app2.conf")
或
//在app.conf里引用
include "app2.conf"
```





## models

可以把公共的功能单独抽取出来作为一个模块,一般会在Model里封装一些公共的方法让不同Controller使用,也可以在Model中实现和数据库打交道.

tools.go

```go
//时间戳转字符串时间
func UnixToDate(timestamp int) string {
    t:=time.Unix(int64(timestamp),0)
    return t.Format("2006-01-02 15:04:05")
}

//时间字符串转时间戳
func DateToUnix(str string) int64 {
	template := "2006-01-02 15:04:05"
	t, err := time.ParseInLocation(template, str, time.Local)
	if err != nil {
		return 0
	}
	return t.Unix()
}

//MD5加密字符串
func MD5(str string) string {
    data:=[]byte(str)
    return fmt.Sprintf("%x\n",md5.Sum(data))
}
```





## controllers

### 发送数据:

```go
type MainController struct {
	beego.Controller 	//继承结构体
}

type User struct {
    //注意如果想用ParseForm解析,label要写form
    //可使用json的label定义json中的名字
    Username string `form:"username"`
    Password string `form:"password"`
    Hobby []string `form:"hobby"`
}

//Get方法是继承下来的
func (c *MainController) Get() {
    //将数据放入模板中
	c.Data["Website"] = "beego.me"
	c.Data["Email"] = "astaxie@gmail.com"
    
    //引用index.tpl模板
	c.TplName = "index.tpl"
    
    //直接写字符串
    //不过写在模板后边会覆盖掉模板的内容
    c.Ctx.WriteString("新闻列表")
    
    //定义一个结构体
    u:=User{
		Username: "aaa",
		Password: "aaab",
		Hobby: []string{"1,2","2,3"},
	}
	//以json的方式传输此结构体,固定写法
	c.Data["json"] = u
	c.ServeJSON()
}
```

### 接收数据:

```go
func (c *MainController) Edit()  {
	//获取get和post表单请求发来的数据
    //put,delete请求同样视为post请求
    //同样还有GetString,GetStrings,GetBool,GetFloat方法
   	id,err := c.GetInt("id")
    username := c.GetString("username")
    password := c.GetString("password")
    
    //表单多选框传入的数据解析为字符串切片
    hobby:=c.GetStrings("hobby")
}

func (c *MainController) Add()  {
	//另一种写法
    //表单数据绑定结构体
    u:=User{}
    //传入结构体的指针
    if err:=c.ParseForm(&u);err!=nil{
        c.Ctx.WriteString("post提交失败")\
        return
	}
}

//接收动态路由的值,正则路由也适用
func (c *ApiController) Get() {
	id := c.Ctx.Input.Param(":id")
	c.Ctx.WriteString(id)
}
```

### 解析xml:

```go
//定义xml解析的结构体
//label需要写xml
type Product struct {
	Title string `xml:"title"`
	Content string `xml:"content"`
}

//xml数据格式
/**
	<?xml version="1.0" encoding="utf-8"?>
	<product>
    	<title>abc</title>
    	<content>123</content>
	</product>
*/

//解析xml数据
//注意:在配置文件里设置 copyrequestbody = true 改完配置后重新运行项目
func (c *GoodsController) Xml() {
    //c.Ctx.Input.RequestBody是byte切片
	str:=string(c.Ctx.Input.RequestBody)
    fmt.Println(str)
	//初始化结构体
	p:=Product{}
	//用go自带的xml包解析xml数据
	if err:=xml.Unmarshal(c.Ctx.Input.RequestBody,&p);err!= nil {
		c.Data["json"] = err.Error()
		c.ServeJSON()
	} else {
        //转成json输出
		c.Data["json"] = p
		c.ServeJSON()
	}
}
```

### 跳转:

```go
//302是状态码
c.Redirect("/",302)

c.Ctx.Redirect(302,"/")

c.TplName = "xxx.html"
```





## routers

### 静态路由:

```go
//router中传入的是结构体指针
beego.Router("/", &controllers.MainController{})
beego.Router("/goods",&controllers.GoodsController{})
beego.Router("/article",&controllers.ArticleController{}) //默认get请求,执行结构体中Get方法
beego.Router("/article/add",&controllers.ArticleController{},"get:AddArticle")//使用get请求访问此结构体下的 AddArticle 方法
beego.Router("/article/edit",&controllers.ArticleController{},"post:EditArticle")//使用post请求访问此结构体下的 EditArticle 方法

//可使用restfull风格
beego.Router("/goods",&controllers.GoodsController{}) 							//get
beego.Router("/goods/add",&controllers.GoodsController{},"post:DoAdd")			//post
beego.Router("/goods/edit",&controllers.GoodsController{},"put:DoEdit")			//put
beego.Router("/goods/delete",&controllers.GoodsController{},"delete:DoDelete")	//delete

beego.Router("/goods/xml",&controllers.GoodsController{},"post:Xml")			//解析xml
```

### 动态路由:

```go
beego.Router("/api/:id",&controllers.ApiController{})
```

### 正则路由:

```go
//http://localhost:8080/cms_123.html
beego.Router("/cms_:id([0-9]+).html",&controllers.CmsController{})
```





## views

### 修改默认模板标签:

app.conf

```go
//修改配置文件
TemplateLeft = "<<"
TemplateRight = ">>"
```

或

main.go

```go
//main方法内添加
beego.BConfig.WebConfig.TemplateLeft = "<<"
beego.BConfig.WebConfig.TemplateRight = ">>"
```

### 绑定数据:

```go
//可识别字符串 数字 布尔
c.Data["name"] = "name"

{{.name}}


//绑定结构体
article:=Article{
    Title:"123",
    Content:"123",
}
c.Data["article"] = article

{{.article.Title}}
{{.article.Content}}
```

### 自定义变量:

```go
//定义变量
{{$xxx := .title}}

//使用变量
{{$xxx}}
```

### 遍历切片:

```go
c.Data["slice"] = []string{"php","java","python","golang"}

<ul>
	{{range $key,$val := .slice}}
		<li>{{$key}}---{{$val}}</li>
	{{end}}
</ul>
```

### 遍历Map:

```go
userinfo := make(map[string]interface{})
userinfo["username"] = "zzz"
userinfo["age"] = 20
userinfo["sex"] = "男"
c.Data["userinfo"] = userinfo

<ul>
	{{range $key,$val := .userinfo}}
		<li>{{$key}}---{{$val}}</li>
	{{end}}
</ul>
```

### 获取Map内容:

```go
userinfo := make(map[string]interface{})
userinfo["username"] = "zzz"
userinfo["age"] = 20
userinfo["sex"] = "男"
userinfo["1"] = map[string]float64{
    "c":4,
}
c.Data["userinfo"] = userinfo

{{map_get .userinfo "username"}}
{{map_get .userinfo "age"}}
{{map_get .userinfo 1 "c"}}//如果key是string类型的数字的话,也可以直接写数字
```

### 遍历结构体切片:

```go
c.Data["articleList"] = []Article{
    {
        Title:"123",
    	Content:"123",
    },
    {
        Title:"1234",
    	Content:"1234",
    },
}

<ul>
	{{range $key,$val := .articleList}}
		<li>{{$key}}---{{$val.Title}}---{{$val.Content}}</li>
	{{end}}
</ul>


//结构体切片的另一种定义方法
//匿名结构体
c.Data["articleList"] = []struct{
    Title string
    Content string
}{
    {
        Title:"123",
    	Content:"123",
    },
    {
        Title:"1234",
    	Content:"1234",
    },
}
```

### 条件判断:

```go
c.Data["isLogin"] = true

{{if .isLogin}}
	
//{{else}}
//{{else if .isHome}}

{{end}}

//比较符号
eq:	==
ne:	!=
lt:	<
le:	<=
gt:	>
ge:	>=

//用法
{{if eq .n1 .n2}}
{{end}}
```

### 自定义模板:

```go
{{define "loop"}}
	<li>{{.Name}}</li>
{{end}}

//"."不要忘记
{{template "loop" .}}
```

### 外部自定义模板:

```go
{{template "/public/footer.html"}}

//后面加"."的话可以把当前页面绑定的数据传输到footer.html页面
{{template "/public/footer.html" .}}
```

### 日期格式化:

```go
now := time.Now()
c.Data["now"] = now


{{date .now "Y-m-d H:i:s"}}
----------------------------
//传入时间戳
func UnixToDate(timestamp int) string{
    t:=time.Unix(int64(timestamp),0)
    return t.Format("2006-01-02 15:04:05")
}

beego.AddFuncMap("unixToDate",UnixToDate)

{{.uinx | unixToDate}}
```

### 截取字符串:

```go
c.Data["title"] = "abcde"


{{substr .title 1 4}}//bcde 下标 长度
----------------------------------
slice:=[]rune("abcde")
slice[1:]
```

### 去除HTML标签:

```go
c.Data["html"]="<h2>123</h2>"

//123
{{.html | html2str}}
{{html2str .html}}
```

### 解析HTML字符串:

```go
c.Data["html"]="<h2>123</h2>"

//h2标题的123
{{.html | str2html}}
{{str2html .html}}
```

### 转义:

```go
c.Data["html"]="<h2>123</h2>"

//&lt;h2&gt;123&lt;/h2&gt;
{{htmlquote .html}}

c.Data["html"]="&lt;h2&gt;123&lt;/h2&gt;"

//<h2>123</h2>
{{htmlunquote .html}}
```

### 引入css:

```go
{{assets_css "/static/css/index.css"}}
= <link rel="stylesheet" href="/static/css/index.css">
```

### 引入js:

```go
{{assets_js "/static/js/base.js"}}
= <script src="/static/js/base.js"/>
```

### 引入配置信息:

```go
//使用方法 {{config configType configKey defaultValue}}
//可选的 configType 有 String, Bool, Int, Int64, Float, DIY
{{config "String" "httpport" ""}}//8080
{{config "String" "appname" ""}}//beegodemo001
```

### 自定义模板函数:

```go
func hello(int string) (out string){
    out = in + "world"
    return
}

beego.AddFuncMap("hi",hello)


{{.val | hi}}
```





## cookie

### 设置cookie(只能设置英文):

```go
func (c *MainController) Get() {
    //1.默认存活时间3600秒,浏览器关闭后cookie消失
	c.Ctx.SetCookie(键,值)
    //cookie:=c.Ctx.GetCookie(键)
    
    //2.设置默认时间后,浏览器关闭后不会消失
	c.Ctx.SetCookie(键,值,过期时间)
    
    //3.设置访问cookie的路径,只有此路由下的路径可以访问
	c.Ctx.SetCookie(键,值,过期时间,路由)"/xxx"
    
    //网站访问的默认端口是80
    //4.cookie的路径Domain(二级域名下也可以访问cookie)  a.itying.com b.itying.com c.itying.com
    c.Ctx.SetCookie(键,值,过期时间,路由,域名)//".itying.com"
    
    //5.secure 为true时,cookie在HTTP中无效,在HTTPS中有效,默认false 
    
    //6.httpOnly 为true时,通过程序(JS脚本,applet等)将无法读取到cookie的信息,防止xss攻击,默认false
}
```

### 设置加密cookie(可设置中文cookie):

```go
//"123456"是秘钥
c.Ctx.SetSecureCookie("123456","username1","李四",1000)
//获取也要同样的秘钥
username,_:=c.Ctx.GetSecureCookie("123456","username1")
```

### 删除cookie:

```go
//设置过期时间为0
c.Ctx.SetCookie("username","",0)

c.Ctx.SetSecureCookie("123456","username1","",0)
```

### 注意事项:

- 使用明文存储,安全性差,可直接查看浏览器获得,重要信息需要加密存储
- 各个浏览器对cookie有限制,大概每个网站只能保存20个cookie,一个最大4kb



## session

### 开启session:

```go
//main.go
beego.BConfig.WebConfig.Session.SessionOn = true
//或
//配置文件
sessionon = true
```

### 设置session:

```go
c.SetSession("user", "zhangsan")
```

### 获取session:

```go
user:=c.GetSession("user")
或
user := c.Ctx.Input.Session("user")
```

### session配置:

```go
//开启session
sessionon = true
//session名称
sessionName = "gyj"
//session过期时间
sessiongcmaxlifetime = 10
//session key
sessionHashKey = "mysessionkey"
```

### 共享session:

**场景:**

Nginx服务器分发不同的请求到不同的服务器,那么不同请求对应的session也就存储到了那台服务器上,但如果下次请求被转发到其他服务器上,session就无法取到.

**方案:**

把session统一保存在服务器共用的redis里,所有服务器取session数据时去redis拿

```go
//连接redis
import _ "github.com/beego/beego/v2/server/web/session/redis"
//选择存储session的工具
beego.BConfig.WebConfig.Session.SessionProvider = "redis"
//工具的连接地址,表示链接的地址，连接池，访问密码，没有保持为空(实测连接池必填,否则无法存储进去)
beego.BConfig.WebConfig.Session.SessionProviderConfig = "127.0.0.1:6379,100"
```





