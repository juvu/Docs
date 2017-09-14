# Supervisor

系统上有许多工作进程在跑，你想要一个统一的入口来管理这些进程，包括状态检查，启动和关闭，出错时警告，及自动重启等。那你就需要一个进程管理工具来帮助你。四个组件：

- supervisord 运行Supervisor的后台服务，它用来启动和管理那些你需要Supervisor管理的子进程，响应客户端发来的请求，重启意外退出的子进程，将子进程的stdout和stderr写入日志，响应事件等。它是Supervisor最核心的部分。
- supervisorctl 相当于supervisord的客户端，它是一个命令行工具，用户可以通过它向supervisord服务发指令，比如查看子进程状态，启动或关闭子进程。它可以连接不同的supervisord服务，包括远程机上的服务。
- Web服务器 这是supervisord的Web客户端，用户可以在Web页面上完成类似于supervisorctl的功能。
- XML-RPC接口 这是留给第三方集成的接口，你的服务可以在远程调用这些XML-RPC接口来控制supervisord管理的子进程。上面的Web服务器其实也是通过这个XML-RPC接口实现的。

## 安装

- 通过clone安装`sudo python setup.py install`
- `pip install supervisor`
- `sudo apt-get install supervisor`

## 配置

生成配置文件`echo_supervisord_conf > supervisord.conf`，其中参数

- [program:theprogramname] 要Supervisor管理的子进程，冒号后面是你可以起的名字

  - command 子进程的运行命令，比如你要监控一个Python程序"app.py"的运行，你可以设置"command=python /home/bjhee/myapp/app.py"。当supervisord服务启动时，该程序也会被自动启动，并作为子进程由supervisord管理。
  - numprocs 同时启动的进程个数，用来实现并发，默认是1。注意如果该参数大于1的话，你必须同时配置"process_name"参数，并且将"%(process_num)s"变量放入"process_name"中，防止多个进程同名导致启动出错。
  - directory 如果配置了这个目录，那子进程运行前，会先切换到这个目录。
  - user 运行该子进程的用户，默认同supervisord服务的启动用户。如果supervisord由root启动，而你又不想给子进程root，你可以配置这个参数。
  - priority 该子进程优先级，决定了启动和关闭子进程的顺序，默认是最大值999。
  - autostart 启动supervisord时，子进程是否自动启动，默认是true。
  - autorestart 当子进程出错退出时，supervisord是否自动将其重启，默认是unexpected，也就是不自动重启，你可以设为true。
  - stdout-logfile 由于子进程由supervisord启动，所以其stdout将无法输出到系统的标准输出上，所以你要将子进程的stdout写入到日志文件中。这个参数指定了该日志文件的位置。
  - stderr-logfile 同上面的stdout-logfile，这里指定了stderr写入的日志文件位置。

## 启动

一个实例：一个Flask的Hello World程序，假设保存在"/home/billy/myapp/app.py"文件中

```
// 配置
[program:app]
command=python /home/billy/myapp/app.py
// 启动，默认会查找配置文件，或者指定
sudo supervisord （-c /home/billy/supervisor/hello.conf）
// 配置嵌套
[include]
files = /home/billy/supervisor/*.conf
//日志文件logfile，默认/tmp/supervisord.log
// 命令行控制
supervisorctl
status/start/stop app
// 加载修改过的命令行
reload
// 客户端配置
[inet_http_server]
port=*:9001
;username=user
;password=123
//进程组：多个子进程可以在同一组内统一管理
[group:appgroup]
programs=app,hello
```