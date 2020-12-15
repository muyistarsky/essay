刚买的服务器需要安装Nginx或者Apache才能通过公网IP进行访问。

需要先安装好软件，Nginx或者Apache、uwsgi。

推荐一个部署Django项目到阿里云的视频教程：https://www.bilibili.com/video/BV1Sb411x7wi


服务器上安装MySQL后需要手动启动：```systemctl start mysqld.service```，
可以设置成开机自启，控制台运行```systemctl enable mysqld.service```。

**提示** ：vim操作，i进入编辑模式，esc退出编辑模式，命令模式下:wq!为保存并退出。

<br><br>

**注意**：在报python的错误时，可以尝试使用python3。

**配置时一定要先单独运行Django试试，可以先运行在0.0.0.0:8000，此时可以直接访问服务器IP:8000看到现象。记得放开服务器8000端口的防火墙。
配置环境时可以打开DEBUG模式，获取详细的错误提示，配置成功后记得关闭DEBUG。**


**语法贴士**：
```
  grep aux | grep uwsgi   查看uwsgi的所有进程
  kill -9 pid号    杀死对应的进程
  uwsgi --ini 文件名.ini   在文件目录下启动通过uwsgi启动项目
  uwsgi --stop 文件名.pid  在文件的目录下停止启动的项目，前提是uwsgi的配置文件中有设置pidfile。
  nohup uwsgi --ini 文件名.ini   可以让项目在断开连接后仍然运行
```

**温馨提示**：在Nginx的配置文件中加上```rror_log 绝对路径/文件名.log error;```可以查看Nginx的错误输出，在进行调试修改时会有一定帮助。

<br><br>
**遇到的错误以及解决方法**：

  可以访问，但是显示bad request ---项目本身有问题  ---查看uwsgi的日志信息有可能获得错误提示，也可以单独运行Django项目，获取详细的
  错误提示，若单独运行Django项目没报错，则可能是settings中的ALLOWED_HOSTS配置有问题。
  
  <br>
  
  uwsgi错误提示为
  ```
    File "testproj/wsgi.py", line 12, in <module>
      from django.core.wsgi import get_wsgi_application
    ModuleNotFoundError: No module named 'django'时
  ```
  
   解决办法：找到Django的位置和pytz的位置，加入到uwsgi的配置文件中。
   ```
   注：pip show django |grep -i Location  显示Django的位置
      pythonpath = django和pytz的位置，分两行写。
   ```
   
   <br>
   
   django报错：django.server:"GET /favicon.ico HTTP/1.1" 500 59
   
   settings中某个list/tuple/str写错了
   
   <br>
   
   成功跑通，但是没有rest_framework和admin样式
   
   解决办法：收集Django的静态文件到指定目录，就是在settings中加入收集路径```STATIC_ROOT = os.path.join(BASE_DIR, "static/") ```，在项目下运行```python manage.py collectstatic ```，配置Nginx，放开静态文件，在Nginx的配置文件的server中加上
   ```
   location /static {        
      alias /data/django/rouboApi/static; 
   }
   ```
   重启Nginx即可看到样式出现。
   
   出现样式后浏览器端和Nginx的错误日志可能会报rest_framework的静态文件没有bootstrap.min.css.map错误，此时只需要删除掉bootstrap.min.css文件内容最后一行/\*…………\*/内容即可。
   
   <br>
   
   uwsgi在服务器内存占用大量内存导致Django项目卡死
   
   原因及解决办法：uwsgi的processes设置过高，要根据服务器的cpu内核数来设置processes的数量，一般最高设置为内核数的两倍。
   
   <br><br>
   
   
      
   
**nginx配置文件**：（直接在Nginx安装目录下的nginx.conf中进行修改，修改部分为server中的键值）
```
  listen 80;  默认就是80，可以不用改。
  server_name 服务器IP或者域名;
  location / {
      uwsgi_pass 127.0.0.1:8000;
      include /etc/nginx/uwsgi_params;  同目录下的uwsgi_params文件
  }
  ```
  <br><br>

      
**uwsgi配置**：manage.py同目录下新建一个任意文件名.ini文件，文件也可放在其他目录下。
```
    [uwsgi]
    chdir = 项目目录,全路径，到manage.py这一级目录
    wsgi-file = mange.py同级目录下有wsgi.py文件的目录，也就是项目目录    示例：visual_project/wsgi.py
    socket = 127.0.0.1:8000
    master = true   主进程
    daemoinze = 存放日志文件的全路径  示例：/tmp/pycharm_project_29/run.log
    disable-logging = true    禁用日志打印
    pidfile = 任意文件名.pid   uwsgi.ini同级目录下的pid文件，用于存放启动主进程后的pid，用于停止uwsgi进程，语法为uwsgi --stop 文件名.pid
    pythonpath = 路径   上面已经说明过，没出现问题则可以不加此项
```
      
