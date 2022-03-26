# Flask web框架

1. Flask 轻量级框架

2. 轻 第三方支持比较全 扩容功能较为方便

3. Django 重量级框架

4. 重 原生组件 全 好

## 安装 

```shell
pip install flask
```



## 启动flask

> 增加路由和视图



## flask中的response

> HttpResponse -return “httpresponse” 返回字符串

> Render - 返回模板页面

> Redirect - 跳转 重定向 response localtion： url



> Flask 特殊封装

- send_file("文件路径") - 打开并返回文件内容 自动识别 文件类型 Content-Type ：text/plan

  ```python
  @app.route("/login")
  def login():
      return send_file('static/1111.jpg')
  ```

- Jsonify({k:v}) -返回标准的json字符串 Content-Type：apllication/json

  ```python
  @app.route("/index")
  			def index():
  			    d = {
  			        "name": "alexdsb"
  			    }
      return jsonify(d)  # / return d
  ```

- Sanic - sonic 索尼克 蓝刺猬 ====flask web

  ```python
  async def login()
  ```

## Flask 中的request

```python
from flask import request
```



request中的一些方法 具体

```python
@app.route("/login",methods=["POST","GET"])
		def login():
		    if request.method=="GET":
		        return render_template("login.html")
		    else:
		        # 请求方式
		        # request.method
		
		        # 获取url 中的请求参数？id=1
		        # print(request.args) #ImmutableMultiDict([('id', '123')])
		        # print(request.args.to_dict()) # 将获取的值转化成字典{'id': '123'}
		        # print(request.args.get("id")) #123
		
		        # form data 中的数据 post 请求提交数据
		        print(request.form)
		        print(request.form.to_dict()) # 得到一个字典
		        request.json # 请求头中带有json格式
		        request.data # 请求头中不带formdata 保存在请求头中的流数据
		        print(request.url) # <http://127.0.0.1:5000/login>
		        print(request.host) #127.0.0.1:5000
		        print(request.host_url) #<http://127.0.0.1:5000/>
		        print(request.path) #127.0.0.1:
		
		        print(request.files) # 直接保存文件 ImmutableMultiDict([('myFile', <FileStorage: '21.html' ('text/html')>)])
		        file = request.files.get("myFile")
		        filename = file.filename # 获取文件名
		        file.save(filename) #保存 保存文件名
        return "200 ok"
```



## Flask 中的原生 session

Session: 

```python
 from flask import flask
```

- 秘钥：secret_key 用于序列化字符串

```python
app.secret_key="%$^&*(HJKJKLSDLJSDK"
```

- 使用session
  - Session["key"] = value
- Session 存到哪里
  - 交由客户端保管机制



## Flask 中的路由

- methods 允许进入当前视图的请求方式

```python
Methods=["POST","GET","DELET","PUT"…..] 8种
```

- endpoint # mapping 对应关系 视图函数 - 路由 必须保证唯一

- Defaults 默认参数

- redirect_to 永久重定向 301 or 308

- strict_slashes 是否严格按照路由匹配规则

- 动态路由规则

  ```python
  @app.route("/home/<int:username>") # 默认类型为string
  		def home(username):
      return f"its 200 {username} ok"
  ```

## Flask 初始化配置



> app = Flask(**name**)

> template_folder # 模板存放路径

> static_folder # 静态文件存放路径

> static_url_path # 静态文件访问路径 ”/开头 /{static_folder}

## Flask 对象配置

```python
app.secret_key="%$^&*(HJKJKLSDLJSDK"
	
# 自动重启 传递错误信息 log级别较低 Debug 级别
app.debug=True
app.config["debug"] = True
app.config["SECRET_KEY"] = "asdjskajdklsajdlskajd"
app.config["PERMANENT_SESSION_LIFETIME"] = 30
app.config["SESSION_COOKIE_NAME"] = "cookie的名字"
app.config["JSONIFY_MIMETYPE"] = "application/json/sadjk"

app.default_config # 非常重要
```



## Flask 蓝图

- 蓝图 - 不能被run 的flask 对象
- From flask import Blueprint
- User_bp = Blueprint("蓝图标识",**name**)

- 蓝图功能：
  1. 功能隔离
  2. 路由视图分割
- Flask 实现web socket 需要实现什么组件？
  - Gevent -websocket-flask-io
  - Werkzukg-websocket flask io
- Utils 是指 数据库连接池
- 在app 中需要 register 蓝图
  - app.register_blueprint(user.user_bp)

## Flask 中的特殊装饰器

- @app.before_request # 再请求进入视图函数之前
- @app.after_request #在请求进入视图函数之后 响应返回客户端

正常情况： before1> before2>before3>after3>after2>after1

异常情况： before2 有返回值 ：：：： before1> before2>after3>after2>after1

- @app.errorhandler

@app.errorhandler(HttpErrorCode) # 重定向返回信息 HttpErrorCode[4xx,5xx]



## Flask 中的CBV -- rest api 规则

```python
class Login(MethodView):
	    def get(self):
	        return render_template('login.html')
	    def post(self):
	        if request.form.get("username")=="lzc":
	            return redirect("/index")
app.add_url_rule("/login",view_func=Login.as_view(name="login"))
```





## Flask 请求上下文



## 智能亲子互动玩具 ——AiToy Angla

1. 需求：

   沟通 互动

   解决 留守儿童与父母之间沟通与感情问题

   手机 屏幕 蓝光伤害眼睛 父母平时工作忙 一般情况下 产生一个时间点

   选择 做一款玩具

2. Toy 功能

   1. 玩具与手机互动

      1. 互动方式

         - App 推送歌曲

           - 通过websocket onmessage 监听西悉尼
           - 收取歌曲文件名
           - 利用歌曲文件名 通过接口 获取文件内容 并播放

         - App 与玩具之间语言消息

           - 主动发起

             - Ai 指令 - 给爸爸发信息（聊天，说话）
             - Ai 模拟 用户发送一条语音消息和聊天信息 - 被动发起聊天
             - 录制语音消息 上传服务器
             - 服务器保存语音消息 返回文件名称
             - 玩具将文件名发送给 app

           - 被动发起

             - 通过websocket onmessage 收到语音消息

             - 收取信息 并播放 全部当前联系人的所有信息 按钮

             - 联系人信息

             - 利用联系人信息 录制语音消息

               

   2. 玩具与玩具之间互动

      ​		羊群效应

      - A - B 产生互动

      - Cdefghijk 看着好玩 cdefghijk 就会让他的父母有限购买此产品

        

   3. 玩具与ai之间的互动

      点播歌曲

      1. 语音指令 语音文件- 百度AI ASR 将语音指令转化成文字指令
      2. NLP 自然语言处理 - 判断关键字
      3. Gensim lsumodel
      4. 稀疏矩阵相似度 判断指令中 最接近的内容
      5. 返回内容信息
      6. 从内容信息中获取内容的id 利用id 获取内容详情

      智能对话

      ​	百科问答

      ​	成语接龙

      ​	脑筋急转弯

      ​	讲故事

      ​	讲笑话

      ​	图灵机器人

      ​				

      1. > 语音指令 语音文件- 百度AI ASR 将语音指令转化成文字指令

      2. > 文字直接发送给图灵机器人

      3. > 图灵机器人返回结果

      4. > 结果通过百度AITTS 文字转语音 语音合成

      5. > 将内容返回给玩具

      6. > 从内容信息中获取内容的id 利用id 获取内容详情

      ​	

3. App 功能：

   1. App 推送歌曲

      1. 实现方式？
      2. Web socket 实时消息
      3. App 发送歌曲名称 到玩具
         - 玩具利用歌曲名称向服务器获取内容详情并播放

   2. 扫描二维码 创建并绑定玩具

      1. 创建二维码 联图二维码API
      2. 通过app 扫描设备二维码
      3. 创建玩具信息

   3. App 控制玩具通讯录

      1. 基于通讯录的IM即使通讯工具
      2. 好友请求
      3. 发送请求 - 被添加方 - 被添加放的绑定app 收取请求
         1. 忽略
         2. 同意 双方添加好友
         3. 拒绝 隐藏好友请求

   4. App 与玩具之间语音消息

      1. App 选择通讯的玩具
      2. App 录制语音消息
      3. 将语音消息上传到服务器 服务器保存语音消息 并返回文件名
      4. App 收到文件名 将文件名发送给玩具
      5. 玩具利用歌曲名称向服务器获取内容详情并播放

   5. 播放歌曲

      HTML5 Plus 中的audio 接口 createPlayer(url)

4. 亮点

   1. Mongodb 数据存储 - 用户画像 json制作数据后期

      1. 利用玩具点播歌曲的log 分析出 玩家的喜好
      2. 利用玩具互动频率log 分析出和哪个小伙伴关系比较亲密

   2. Ai 人工智能

      1. 通过百度大脑
      2. ASR语音
      3. TTS 语音合成
      4. NLP 自然语言合成

   3. HTML5Plus+Mui

      1. HTML5PLUS 硬件驱动调用 和 系统调用
      2. Mui 布局框架

   4. Websocket

      1. 基于长时间的实时通讯

   5. 机器学习 - 微量性 30个数据 纳米级机器学习

      1. Gensim 机器学习库 很多语言模型 很多语言模型算法

      2. Lsimodel 语言模型 稀疏矩阵相似度

         

         获取内容【内容1，内容2】

         分词 Doc2Bo

         Lsimodel 训练样本数据

         Lsimodel 转换用户数据

         稀疏矩阵相似度 用户数据与样本数据的相似度