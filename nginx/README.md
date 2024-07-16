# Nginx

Nginx（发音为“engine X”）是一个开源的、高性能的Web服务器软件，也可以用作反向代理服务器、负载均衡器、HTTP缓存以及通用的代理服务器。Nginx在处理并发连接和高流量负载方面表现出色，因此在构建稳定、高效的网络应用程序和网站时非常受欢迎。<br>

安装和使用Nginx（Engine-X）在Ubuntu 18.04.6上相对比较简单。本文讲解安装、Nginx的基本使用和常见用法：<br>

- [Nginx](#nginx)
  - [检查Nginx服务状态:](#检查nginx服务状态)
  - [安装Nginx并启动:](#安装nginx并启动)
    - [Nginx无法启动的解决方案:](#nginx无法启动的解决方案)
    - [`systemctl start nginx` 和 `service nginx start` 的区别:](#systemctl-start-nginx-和-service-nginx-start-的区别)
  - [停止Nginx服务:](#停止nginx服务)
  - [让Nginx重新加载配置:](#让nginx重新加载配置)
  - [Nginx配置项的选择:](#nginx配置项的选择)
    - [情况描述:](#情况描述)
    - [具体解释:](#具体解释)
    - [为个人服务配置域名时的Nginx配置:](#为个人服务配置域名时的nginx配置)
      - [情况描述:](#情况描述-1)
      - [解决方案:](#解决方案)
    - [前端dist文件利用nginx起服务:](#前端dist文件利用nginx起服务)
      - [其他注意事项](#其他注意事项)
      - [如果服务要迁移，前端dist文件要如何处理:](#如果服务要迁移前端dist文件要如何处理)
  - [Nginx的基本使用：](#nginx的基本使用)
  - [网址的批量映射：](#网址的批量映射)


## 检查Nginx服务状态:

安装Nginx前，要先检查服务器上是否已安装了Nginx。不要因为自己的缘故，影响其他人的服务。检查Nginx服务状态的指令如下:<br>

```bash
sudo systemctl status nginx
```

终端显示如下:<br>

```log
(base) root@iZ2zea5v77oawjy2qz7c20Z:/etc# sudo systemctl status nginx
Unit nginx.service could not be found.
(base) root@iZ2zea5v77oawjy2qz7c20Z:/etc#
```

如果Nginx已安装并运行，你会看到类似以下输出:<br>

```log
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since ...
```


## 安装Nginx并启动:

1. 打开终端。

2. 更新软件包列表：

```bash
sudo apt update
```

注意⚠️⚠️⚠️：更新软件包列表是让系统获取各种库的信息，并不会升级或降低个人环境中的库版本。<br>

3. 安装Nginx:

```bash
sudo apt install nginx
```

4. 安装完成后，Nginx将会自动启动。你可以通过以下命令检查Nginx的运行状态：

```bash
sudo systemctl status nginx
```

### Nginx无法启动的解决方案:

如果上述指令你无法使用，Nginx 没有正常启动，可以尝试使用下列指令启动Nginx：<br>

```bash
sudo systemctl start nginx
```

或者使用：<br>

```bash
sudo service nginx start
```

### `systemctl start nginx` 和 `service nginx start` 的区别:

`sudo systemctl start nginx` 和 `sudo service nginx start` 这两个命令实际上都是用来启动 Nginx 服务的，但它们之间存在一些区别，主要是因为不同的服务管理系统。<br>

`sudo systemctl start nginx` 是使用 `Systemd` 系统管理工具来控制服务的命令，在现代的 `Linux` 发行版中，`Systemd` 是常见的 `init` 系统，用于管理系统进程和服务。<br>

`sudo service nginx start` 是使用 `System V (SysV) init` 系统的服务管理命令。`SysV init` 系统是☕️☕️☕️旧式的 `init` 系统，用于在 `Linux` 发行版中管理系统服务，但在现代系统中逐渐被 `Systemd` 取代。<br>

`service` 命令提供了一种简化的接口来启动、停止和管理服务，相对来说 `systemctl` 命令提供了更多的功能和灵活性。<br>


## 停止Nginx服务: 

如果想要停止Nginx服务可以使用下列指令:<br>

```bash
sudo systemctl stop nginx
```

或者使用：<br>

```bash
sudo service nginx stop
```


## 让Nginx重新加载配置:

如果你修改了Nginx的配置文件，为了让Nginx重新加载配置，你可以使用以下命令:<br>

```bash
sudo systemctl reload nginx
```

🚨但重新加载配置前需要先检查你的配置语法是否正确:<br>

```bash
sudo nginx -t
```

如果配置文件中没有语法错误，你将会看到类似以下内容的输出：<br>

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

如果存在语法错误，它们会被显示出来，你需要根据错误信息来修复配置文件。<br>

🔥如果配置文件语法没有问题，重新加载Nginx配置，使更改生效即可：<br>

```bash
sudo systemctl reload nginx
```

上面的指令用于不关闭 Nginx 的情况使修改后的配置文件生效。如果你的 Nginx 没有运行，直接启动它等于相同的效果：<br>

```bash
sudo systemctl start nginx
```


## Nginx配置项的选择:

### 情况描述:

有些人说修改nginx配置需要修改 `/etc/nginx/nginx.conf`，有些人说要要在 `/etc/nginx/conf.d/` 添加自己的配置。不同的选择，总会让人很懵。那这两个人谁说的对呢？是有不同场景的使用限制吗？<br>

### 具体解释:

你的疑惑很正常，因为Nginx的配置文件有不同的层级和用途，具体使用哪个文件修改配置取决于你的需求和场景。下面是对这几种文件的解释及其使用场景：<br>

1. **`/etc/nginx/nginx.conf`**:

- 这是Nginx的主配置文件，包含全局配置和一些默认配置。一般用于定义全局设置，如用户权限、工作进程数量、日志路径等。

- 适用场景：当需要修改全局设置或Nginx的核心配置时。

2. **`/etc/nginx/conf.d/`**:

- 这个目录通常用于存放额外的配置文件，默认Nginx会加载这个目录下的所有配置文件。这些文件通常用于定义特定的虚拟主机配置或其他模块的配置。

- 适用场景：当你需要为不同的网站（虚拟主机）或者特定服务模块单独配置时，可以在这个目录下创建新的配置文件（通常以`.conf`结尾）。

3. **其他配置文件**:

- 在Nginx的主配置文件中，可以使用`include`指令引入其他配置文件。例如，可以在`nginx.conf`中包含`sites-available`或`sites-enabled`目录下的配置文件。这些目录通常用于存放虚拟主机配置。

- 适用场景：根据个人或组织的习惯，配置文件可以放在不同的目录中，以便于管理和维护。例如，在Debian/Ubuntu系统中，通常使用`/etc/nginx/sites-available/`和`/etc/nginx/sites-enabled/`目录。

### 为个人服务配置域名时的Nginx配置:

#### 情况描述:

我为我的服务器配置了一个域名(peilongchencc.cn)，我想要把这个域名和`http://localhost:7860`绑定，我该配置哪个呢？配置好后，`http://localhost:7860/chat`会是什么情况？<br>

#### 解决方案:

为了将你的域名 `peilongchencc.cn` 绑定到 `http://localhost:7860`，你需要在 Nginx 中配置一个反向代理。具体来说，你可以在 `/etc/nginx/conf.d/` 目录下创建一个新的配置文件，或在已有的配置文件中添加相应的配置。具体操作如下:<br>

1. **创建或修改配置文件**：

在 `/etc/nginx/conf.d/` 目录下创建一个新的配置文件，例如 `peilongchencc.cn.conf`：<br>

```bash
sudo vim /etc/nginx/conf.d/peilongchencc.cn.conf
```

然后在文件中添加以下配置：<br>

```nginx
server {
    listen 80;
    server_name peilongchencc.cn;

    location / {
        proxy_pass http://localhost:7860;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

这个配置将把所有对 `peilongchencc.cn` 的请求代理到 `http://localhost:7860`。<br>

2. **检查配置文件语法**：

在终端中运行以下命令以检查 Nginx 配置文件的语法：<br>

```bash
sudo nginx -t
```

如果没有错误，将显示类似于 `syntax is ok` 和 `test is successful` 的信息。<br>

3. **重载 Nginx 配置**：

语法检查通过后，重载 Nginx 配置以应用新的更改：<br>

```bash
sudo systemctl reload nginx
```

4. **验证**:

配置完成后，你可以在浏览器中访问 `http://peilongchencc.cn`。Nginx 会将请求代理到 `http://localhost:7860`。同样，访问 `http://peilongchencc.cn/chat` 会被代理到 `http://localhost:7860/chat`。

> 基本上配置 Nginx 只需要将你的域名 `peilongchencc.cn` 代理到 `localhost` 和相应的端口即可。

### 前端dist文件利用nginx起服务:

前端的`dist`文件通常是前端项目打包后的静态资源文件，需要通过Nginx进行托管和访问。为此，你可以在Nginx的配置文件中设置一个服务器块（server block）来处理这些静态文件。通常，这类配置文件会放在`/etc/nginx/conf.d/`目录下。<br>

下面是一个典型的配置示例，假设你的前端`dist`文件目录位于`/var/www/mywebsite/dist`：<br>

1. **创建新的配置文件**：

在`/etc/nginx/conf.d/`目录下创建一个新的配置文件，例如`mywebsite.conf`：<br>

```bash
sudo vim /etc/nginx/conf.d/mywebsite.conf
```

2. **配置文件内容**：

在文件中添加如下配置：<br>

```nginx
server {
    listen       8000;

    root   /data/mywebsite/dist;
    index  index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

解释：<br>

- `listen 8000;`：监听8000端口，处理HTTP请求。
- `root   /data/mywebsite/dist;;`：指定静态文件的根目录。
- `index index.html;`：指定默认文件。
- `location /`：处理根路径请求。
    - `try_files $uri $uri/ /index.html;`：尝试按顺序查找文件，如果文件不存在，则返回`index.html`，这对于SPA（单页应用）非常重要。

3. **检查配置文件语法并重启Nginx**：

在添加或修改配置文件后，检查配置文件的语法是否正确：<br>

```bash
sudo nginx -t
```

如果没有错误，重新加载Nginx配置：<br>

```bash
sudo systemctl reload nginx
```

#### 其他注意事项

- **HTTPS 配置**：如果你需要使用HTTPS，还需要配置SSL证书，可以在同一个server块中添加相应的配置：

```nginx
listen 443 ssl;
ssl_certificate /path/to/your/certificate.crt;
ssl_certificate_key /path/to/your/certificate.key;
```

- **反向代理**：如果你的前端需要与后端API通信，可以在同一个server块中配置反向代理：

```nginx
location /api/ {
    proxy_pass http://backend_server;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

#### 如果服务要迁移，前端dist文件要如何处理:

假设原来前端的文件(dist文件)和后端的代码部署在不同的服务器，前端代码中（例如 `.js` 或 `.html` 文件）通过 `http://8.140.203.xxx:8848/bank_chatbot` 访问你的服务。<br>

现在，领导要求前端的服务需要和你后端的服务部署在同一台服务器。你需要:<br>

1. 修改IP:

将前端代码中的 `http://8.140.203.xxx:8848/bank_chatbot` 修改为 `http://localhost:8848/bank_chatbot`。

2. 配置Nginx:

配置Nginx的方法前面已经讲过了，详情可参考上面的内容。

3. 重启Nginx;

4. 打开网址:

利用网址加域名的方式即可打开前端界面，如果`dist`文件中加了路由，可以使用类似下列方式打开界面:<br>

```log
http://8.140.203.xxx:8000/Index
```

> 8101是Nginx中单独给前端网页配的端口。


## Nginx的基本使用：

接下来以**Nginx反向代理**为例，讲解 Flask + Nginx 的使用：<br>

> 其实无论你使用的是 Flask、Fast-API、Sanic或其他Web应用程序框架，和 `Nginx` 结合使用的道理是一样的。

1. 确定自己的路由、端口信息，然后启动自己的Flask服务：

假设你程序入口的代码如下，端口号为7711，访问的URL为 `http://localhost:7711/nlp-server/`：<br>

```python
from flask import Flask, Blueprint

# 创建一个 Flask 应用实例
app = Flask(__name__)

# 创建一个 Blueprint 并设置前缀
nlp_server_blueprint = Blueprint('nlp_server', __name__, url_prefix='/nlp-server')

# 在 Blueprint 中定义路由
@nlp_server_blueprint.route('/')
def hello():
    return 'Hello, NLP Server!'

# 注册 Blueprint 到应用
app.register_blueprint(nlp_server_blueprint)

# 运行应用
if __name__ == '__main__':
    app.run(port=7711)
```

2. 打开 Nginx 配置文件，

根据 Nginx 在 Ubuntu 上的默认位置打开 Nginx 配置文件：<br>

```bash
sudo vim /etc/nginx/nginx.conf
```

3. 在 `http` 模块内添加以下配置：(网址的批量映射请看下一节内容)

```python
server {
    isten 80;
    server_name localhost;
    
    location /nlp/ {
        proxy_pass http://localhost:7711/nlp-server/;
        }
}
```
该配置将监听端口 `80`，并将 `/nlp/ans` 路径代理到 Sanic 服务的 `localhost:7711/nlp-server/` 路径。意思就是：你在访问时只需要输入 `http://localhost:80/nlp/` 就相当于访问了 `http://localhost:7711/nlp-server/`。<br>
😀😀😀值得一提的是，Nginx 反向代理并不会拖慢程序反应速度。<br>

4. 检查配置语法：

```bash
sudo nginx -t
```

如果配置文件中没有语法错误，你将会看到类似以下内容的输出：<br>

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

如果存在语法错误，它们会被显示出来，你需要根据错误信息来修复配置文件。<br>

5. 重新加载Nginx配置:

如果配置文件语法没有问题，重新加载Nginx配置，使更改生效即可：<br>

```bash
sudo systemctl reload nginx
```

上面的指令用于不关闭 Nginx 的情况使修改后的配置文件生效。如果你的 Nginx 没有运行，直接启动它等于相同的效果：<br>

```bash
sudo systemctl start nginx
```

现在你就可以通过 `http://localhost:80/nlp/` 访问自己的服务了～<br>


## 网址的批量映射：

```log
server {
    listen       8082;
    server_name  localhost;
    
    # 修改以bp1为前缀的所有网址的映射
    location /nlp/ {
        proxy_pass http://localhost:7711/bp1/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    # 修改以bp2为前缀的所有网址的映射
    location /irm/ {
        proxy_pass http://localhost:7744/bp2/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}                          
```