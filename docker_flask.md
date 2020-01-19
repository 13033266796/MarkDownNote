# Docker

## 容器
1. 容器是什么
   百度百科：
    &emsp;&emsp;Docker 容器是一个开源的应用容器引擎，让开发者可以以统一的方式打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何安装了docker引擎的服务器上（包括流行的Linux机器、windows机器），也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）。几乎没有性能开销,可以很容易地在机器和数据中心中运行。最重要的是,他们不依赖于任何语言、框架包括系统。
    
   理解：
   &emsp;&emsp;类似linux系统环境，运行和隔离应用。容器从镜像启动的时候，docker会在镜像的最上一层创建一个可写层，镜像本身是只读的，保持不变。

2. 容器有什么作用
   
3. 如何使用容器
   1. 先获取一个镜像
        如果我们本地没有 ubuntu 镜像，我们可以使用 docker pull 命令来载入 ubuntu 镜像
        > docker pull ubuntu"版本号"
   2. 启动容器
        > docker run -it ubuntu /bin/bash
    
        i:交互式操作
        t:终端
        "/bin/bash"： 命令
        <br>
   3. 停止容器
        > docker stop <容器 ID>

        容器ID 可以通过docker ps -a 查看
        除了ID之外还包含了IMAGE、COMMAND、CREATED、STATUS、PORTS、NAMES等信息

   4. 进入容器
        > docker attach
        > docker exec 退出容器终端，不会导致容器的停止。

   5. 导入和导出容器
        导出
        > docker export <容器 ID> > ubuntu.tar

        导入
        > cat docker/ubuntu.tar | docker import - test/ubuntu:v1

   6. 删除容器
        > docker rm -f <容器 ID>

4.LXC(Linux containers)
    &emsp;&emsp;基于容器的操作系统层级的虚拟化技术

## 镜像
1. 镜像是什么
    &emsp;&emsp;镜像由多个层组成，每层叠加之后，从外部看来就如一个独立的对象。镜像内部是一个精简的操作系统（OS），同时还包含应用运行所必须的文件和依赖包。
    &emsp;&emsp;构建容器的基础，是一种分层结构的文件系统。
2. 镜像有什么作用
    &emsp;&emsp;包含应用/服务运行所必需的操作系统和应用文件,不包含内核,容器都是共享所在 Docker 主机的内核。
3. 如何使用镜像
    下载镜像, 默认情况下，镜像会从 Docker Hub 的仓库中拉取。
    > docker image pull [仓库]:[标签]

    查看本地镜像列表
    > docker images

    使用版本为18.04的ubuntu系统镜像运行容器
    > docker run -t -i ubuntu:18.04 /bin/bash 

    删除镜像
    > docker image rm

## 仓库
1. 仓库是什么
2. 仓库有什么作用
    镜像仓库类似于Git中仓库的概念。一个仓库中可以保存很多镜像，或者说很多版本的镜像。也和Git一样，Docker Hub也通过标签（tag）来标签镜像的不同版本。如：ubuntu镜像的仓库中就包含了12.04、14.04、16.10、trusty、latest等不同的镜像版本。
3. 如何使用仓库
4. 仓库类型
    &emsp;&emsp;用户仓库：由用户创建的仓库
    &emsp;&emsp;顶层仓库：由Doker官方创建和维护


    登录Docker Hub后，可以通过Create-Create Repository链接创建用户仓库，下载用户仓库中的镜像与项层仓库下载规则相同：
    > docker pull [用户名]/[仓库名]:版本号


## 应用容器化
1.编写应用代码

2.创建一个Dockerfile，其中包括当前应用的描述、依赖以及该如何运行这个应用
    &emsp;&emsp;Dockerfile的用途：对当前应用的描述；指导Docker完成应用的容器化创建一个包含当前应用的镜像）。

``` python
 FROM [镜像] 基础镜像层
 LABEL maintainer="指定维护者"
 RUN apk add --update nodejs nodejs-npm
 # 指令使用 alpine 的 apk 包管理器将 nodejs 和 nodejs-npm 安装到当前镜像之中。
```

3.对该Dockerfile执行docker image build命令


4.等待Docker将应用程序构建到Docker镜像中




# Flask
&emsp;&emsp;Flask是一个使用 Python 编写的轻量级 Web 应用框架。其 WSGI 工具箱采用 Werkzeug ，模板引擎则使用 Jinja2 。Flask使用 BSD 授权，没有默认使用的数据库、窗体验证工具。
## app
&emsp;&emsp;Flask对象实例
    

## 装饰器
```python
    @app.route('/) # 用于index函数绑定到 URL（“/”）
    def index():
        pass

```
## url
&emsp;&emsp;不同的url规则调用不同的函数，实现不同的功能。
重定向：
```python
    @app.route('/projects/') 
    @app.route('/about') # 在地址栏输入/about/报错 flask已经自动在结尾添加了一个“/”
```
## template
模板渲染：render_template("index.html")


# docker-compose flask
1. 在项目中编写Dockerfile
    ```python
        FROM python:3.6                     # 基础镜像
        ADD . /code                         # 将flask项目所有文件拷贝
        WORKDIR /code                       # 将后续未执行的文件工作目录设置为 /code
        RUN pip install -r requirements.txt # 运行 pip 安装flask项目所需要的依赖
        CMD python app.py                   # 执行flask项目
    ```
2. 在Docker机中 用Dockerfile创建相应镜像
3. 创建docker-compose.yml 文件 定义服务
    ```python
        version: '2'
        services:
            web:
                build: .
                ports:
                    - "5000:5000"
                volumes:
                    - .:/code
                depends_on:
                    - redis
            redis:
                image: redis
    ```
4. 安装docker-compose
   > sudo apt install docker-compose
5. 启动 docker-compose up
   启动过程中 pull python太慢， 解决办法：将之前pull 中断的python镜像删除

6. 关闭 docker-compose stop
    若用 docker-compose up -d 启动，必须使用docker-compose stop关闭，
    直接用 docker-compose up 启动，在终端 Ctrl + C 结束即可。


# Restful

## Rest架构的主要原则
&emsp;&emsp;网络上的所有事物都被抽象为资源

&emsp;&emsp;每个资源都有一个唯一的资源标识符

&emsp;&emsp;同一个资源具有多种表现形式(xml,json等)

&emsp;&emsp;对资源的各种操作不会改变资源标识符

&emsp;&emsp;所有的操作都是无状态的

## 资源操作
    GET         ->      Read
    POST        ->      Create
    PUT         ->      Update
    DELETE      ->      Delete

## flask_restplus

#### 示例
```python
from flask import Flask, request
from flask_restplus import Resource, Api

app = Flask(__name__) # 实例化一个app对象
api = Api(app)        # 将app和restplus的Api绑定
UserInfo = {}         # 资源

# app绑定api后 路由操作通过api
@api.route('/restplus')
class TestRestplus(Resource):
    def get(self):
        return {'key': "this is function 'get' "}
    def post(self):
        return {'key': "this is function 'post' "}

@api.route('/test1/<string:user_id>')
class TestRestplus2(Resource):
    def get(self, user_id):             # 获取资源
        return {user_id : UserInfo[user_id]}

    def put(self, user_id):             # 更新资源
        UserInfo[user_id] = request.form.get('data')
        return {user_id : UserInfo[user_id]}

    def post(self, user_id):            # 创建资源
        UserInfo[user_id] = request.get('data')
        return {user_id: UserInfo[user_id]}

    def delete(self, user_id):          # 删除资源
        UserInfo.pop(user_id)
        return 'delete successfully !'
```
#### 端点（Endpoints）
&emsp;&emsp;可以向Api对象的add_resource()方法或route()装饰器中传入多个URL，这样每个URL都将会路由到该资源上。
```python
api.add_resource(HelloWorld, '/hello', '/world')

# 或者下面装饰器方式，二者等价

@api.route('/hello', '/world')
class HelloWorld(Resource):
    pass
```

&emsp;&emsp;也可以将URL中的部分内容设置成变量，以此来匹配资源方法
```python
api.add_resource(Todo, '/todo/<int:todo_id>', endpoint='todo_ep')

# 或者下面装饰器方式，二者等价

@api.route('/todo/<int:todo_id>', endpoint='todo_ep')
class HelloWorld(Resource):
    pass

# 这样，URL为/todo/1、/todo/2等以/todo/加一个int型整数的URL都可以路由到该资源
```
#### 数据格式化（Data Formatting）
&emsp;&emsp;Flask-RESTPlus提供了fields模块和marshal_with()装饰器。类似于Django ORM
```python
model = api.model('Model', {
    'task': fields.String,
    'uri': fields.Url('todo_ep',absolute=True) # absolute参数表示生成的url是否是绝对路径
})

@api.route('/todo',endpoint='todo_ep')
class Todo(Resource):
    @api.marshal_with(model)
    def get(self, **kwargs):
        return TodoDao(todo_id='my_todo', task='Remember the milk')
```
#### 数据顺序保留
默认情况下，字段顺序并未得到保留，因为它会损耗性能。不过，如果你确实需要保留字段顺序，那么可以向类或函数传入一个ordered=True的参数项，以此强制进行顺序保留：
<br>
&emsp;&emsp;Api全局保留：api = Api(ordered = True)
<br>
&emsp;&emsp;Namespace全局保留：ns = Namespace(ordered=True)
<br>
&emsp;&emsp;marshal()局部保留：return marshal(data, fields, ordered=True)
```python  
@api.route('/todo',endpoint='todo_ep')
class Todo(Resource):
    # @api.marshal_with(model)
    def get(self, **kwargs):
        return api.marshal(TodoDao(todo_id='my_todo', task='Remember the milk', developer='Tom'),model,ordered=False)
```

#### 参数解析（Argument Parsing）
```python
# 部分
from flask_restplus import reqparse

parser = reqparse.RequestParser()
parser.add_argument('rate', type=int, help='Rate to charge for this resource')
    # 参数名为rate，数据类型为int，请求时必须发送此参数，如果验证不通过时将会返回help指定的信息。
args = parser.parse_args()

todos = {
    '1':'eat',
    '2':'sleep'
}

@api.route('/<string:todo_id>')
class TodoSimple(Resource):
    def get(self, todo_id):
        return {todo_id: todos[todo_id]}

    def put(self, todo_id):
        args = parser.parse_args()
        todos[todo_id] = request.form['data']
        return {todo_id: todos[todo_id]}
```



