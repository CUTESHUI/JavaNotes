## Curl

#### 简介

- command line tool and library for transferring data with URLs
- curl 在命令行下工作，利用 URL 的语法进行数据的传输
- 简单理解：用 curl 去发出网络请求，然后得到和提取数据
- 支持的协议有`30+`种，最常使用的有`FILE、FTP、HTTP、HTTPS`等



### -i / --include

- 输出时包括 response 头信息
- 例子

```bash
curl -i https://www.baidu.com
```



### -I / --head

- 输出时只显示请求头信息
- 例子

```bash
curl -I https://www.baidu.com
```



#### -X / --request <command> 参数

- 指定什么命令，如  GET、POST
- 例子

```bash
curl -X GET http://localhost:8080/search?data=123  # -X GET是可选的，也就是默认
curl -X POST -d "name=shui&age=18" http://localhost:8080/search
curl -X PUT -d "name=shui&age=18" http://localhost:8080/uodate
```



#### -d / --data <data> 参数

- 使用 POST 方式向服务器发送数据，因此在使用 -d 的时候，可以省略  -X POST
- 使用 -d 时，将使用  Content-type:application/x-www-form-urlencoded 方式发送数据
- 想使用 JSON 形式 post 数据，可以使用 -H 指定头部类型
- 例子

```bash
# 等价用法
curl -X POST --data "name=shui&age=18" 'localhost:8080/search'
curl --data "name=shui&age=18" 'localhost:8080/search'
curl -d "name=shui&age=18" 'localhost:8080/search'

# 使用 JSON 形式
curl -H "Content-Type:application/json" -X POST --data '{"name": "shui", "age": "18"}' http://localhost:8000/search
```



#### -H / --header <line>  参数

- 自定义头信息传递给服务器
- 例子

```bash
curl -H "Host:192.168.0.1" -H "accept-language:zh-cn" URL
```



#### -c/--cookie-jar <file>  参数

- 操作结束后把 cookie 写入到这个文件中
- 例子

```bash
curl -d "name=zhangsan&password=123" http://localhost:8080/login -c ./cookie
```



#### -b / --cookie <name=string/file> 参数

- cookie 字符串或文件读取位置
- 例子

```bash
# cookie 文件
curl http://localhost:8080/login -b ./cookie

# 直接指定 cookie
curl --cookie "name=zhangsan" http://localhost:8080/login
```



#### -F / --form <name=content>/--form-string <name=string> 参数

- 模拟 http 表单提交数据，curl 可以通过`-F`命令来以`Content-Type:multipart/form-data`的形式向 server post 数据，该命令允许提交二进制文件等。可以使用`@`前缀来制定提交的内容为一个文件，也可以使用 < 来提交文件中的内容
- 例子

```bash
# 向服务器上传一个图片，图片的表单 name 为 profile，内容为 protrait.jpg 的二进制
curl -F prefile=@portrait.jpg https://example.com/upload
```



#### -O / --remote-name

- 把输出写到该文件中，保留远程文件的文件名
- 例子

```bash
curl http://man.linuxde.net/text.iso --silent -O
```

 

#### -o / --output

- 把输出写到指定文件中，默认输出地址为当前所在地址
- 例子

```bash
# 选项 -o 将下载数据写入到指定名称的文件中，并使用 --progress 显示进度条：
curl http://man.linuxde.net/test.iso -o filename.iso --progress
######################################### 100.0%
```



#### -s / --silent

- 静默模式，不输出任何东西。
- 例子

```bash
curl http://man.linuxde.net/text.iso --silent -O
```

