## 环境说明
* 进行本文操作前需己搭建好的环境      
  - linux系统，我用的是openEuler          
  - Python 3.7.9
  - pip 20.1.1
  - virtualenv 20.0.31
  - nginx 1.16.0
  - SQLite 3.33.0
* 后面进行安装的环境              
  - asgiref 3.2.10
  - Django 3.1.1
  - Pillow 7.2.0
  - pytz 2020.1
  - sqlparse 0.3.1
  - uWSGI 2.0.19.1

## 搭建项目
* 建立一个虚拟环境
  - 建立myapp文件，并在文件下建env和project文件，分别放虚拟环境和程序代码
  - 在env下创建虚拟环境      
    <font color="#DC143C">virtualenv -p python3 py3env</font>
  - 启动虚拟环境     
    <font color="red">source py3env/bin/activate</font>
* 迁移windows下的pip环境
  - 获取pycharm中的pip环境，并存到env.txt中         
    pip freeeze -> env.txt
  - 将env.txt传到linux中，并安装里面的环境     
    pip install -r env.txt
    
## 项目代码上传
* 项目代码测试
  - 将代码上传到/myapp/project/文件下，运行python manage.py runserver查看代码是否有错误
* 部分代码修改（修改settings.py文件）
  - DEBUG = False 
  - ALLOWED_HOSTS = ['*',  ]
  - 添加STATIC_ROOT = os.path.join(BASE_DIR, "static/"), 退出并运行 python manage.py collectstatic 生成静态文件
  
## uwsgi安装和配置
* 安装uwsgi
  - pip install uwsgi
* uwsgi配置
  - 在/myapp/project/下建立conf和logs文件夹，在conf文件夹下建立uwsgi.ini文件
    [uwsgi]  
    chdir = /myapp/project/work/
    module = work.wsgi  
    home = /myapp/env/py3env
    master = true
    processes = 4  
    theads = 2  
    socket = 100.100.228.240:8001
    chmod-sock = 666  
    vacuum = true
    daemonize = /myapp/project/logs/uwsgi.log 
    logfile-chmod = 666 

  
## nginx安装和配置
* nginx安装
  - 将nginx的安装包传到linux并解压     
    tar zxvf nginx-1.6.2.tar.gz
  - 进入安装包目录
    cd nginx-1.6.2
  - 编译安装  
    ./configure    
    make && make install   
* nginx配置
  -在/myapp/project/conf文件夹下建立nginx.conf文件
   
      user  root;
      worker_processes  1;

      error_log  /myapp/project/logs/error.log;
      error_log  /myapp/project/logs/error.log  notice;
      error_log  /myapp/project/logs/error.log  info;

      #pid        logs/nginx.pid;


      events {
          worker_connections  1024;
      }

      http {
          include       /usr/local/nginx/conf/mime.types;
          default_type  application/octet-stream;

          #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
          #                  '$status $body_bytes_sent "$http_referer" '
          #                  '"$http_user_agent" "$http_x_forwarded_for"';

          #access_log  logs/access.log  main;

          sendfile        on;
          #tcp_nopush     on;

          #keepalive_timeout  0;
          keepalive_timeout  65;

          #gzip  on;

          server {
              listen       9000;
              server_name  100.100.228.240;

              #charset utf-8;

              #access_log  logs/host.access.log  main;


              location /static {
                  alias /myapp/project/work/static/; 
              }

              location / {
                  uwsgi_pass  100.100.228.240:8001;
                  include /usr/local/nginx/conf/uwsgi_params;
              }

              #error_page  404              /404.html;

              # redirect server error pages to the static page /50x.html
              #
              error_page   500 502 503 504  /50x.html;
              location = /50x.html {
                  root   html;
              }

              # proxy the PHP scripts to Apache listening on 127.0.0.1:80
         
              #location ~ \.php$ {
              #    proxy_pass   http://127.0.0.1;
              #}
              # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
       
              #location ~ \.php$ {
              #    root           html;
              #    fastcgi_pass   127.0.0.1:9000;
              #    fastcgi_index  index.php;
              #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
              #    include        fastcgi_params;
              #}

              # deny access to .htaccess files, if Apache's document root
              # concurs with nginx's one
              
              #location ~ /\.ht {
              #    deny  all;
              #}
          }

          # another virtual host using mix of IP-, name-, and port-based configuration
         
          #server {
          #    listen       8000;
          #    listen       somename:8080;
          #    server_name  somename  alias  another.alias;

          #    location / {
          #        root   html;
          #        index  index.html index.htm;
          #    }
          #}


          # HTTPS server
          #
          #server {
          #    listen       443 ssl;
          #    server_name  localhost;

          #    ssl_certificate      cert.pem;
          #    ssl_certificate_key  cert.key;

          #    ssl_session_cache    shared:SSL:1m;
          #    ssl_session_timeout  5m;

          #    ssl_ciphers  HIGH:!aNULL:!MD5;
          #    ssl_prefer_server_ciphers  on;

          #    location / {
          #        root   html;
          #        index  index.html index.htm;
          #    }
          #}

      }
## 项目启动









  
