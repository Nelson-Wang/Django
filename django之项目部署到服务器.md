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
    ```diff
    - virtualenv -p python3 py3env
    ```
  - 启动虚拟环境     
    ```diff
    - source py3env/bin/activate
    ```
* 迁移windows下的pip环境
  - 获取pycharm中的pip环境，并存到env.txt中        
    ```diff
    - pip freeeze -> env.txt
    ```
  - 将env.txt传到linux中，并安装里面的环境     
    ```diff
    - pip install -r env.txt
    ```
## 项目代码上传
* 项目代码测试
  - 将代码上传到/myapp/project/文件下，运行python manage.py runserver查看代码是否有错误
* 部分代码修改（修改settings.py文件）
  - DEBUG = False 
  - ALLOWED_HOSTS = ['*',  ]
  - 添加STATIC_ROOT = os.path.join(BASE_DIR, "static/"), 退出并运行 python manage.py collectstatic 生成静态文件
  
## uwsgi安装和配置
* 安装uwsgi
    ```diff
    - pip install uwsgi
    ```
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
    ```diff
    - tar zxvf nginx-1.6.2.tar.gz
    ```
  - 进入安装包目录
    ```diff
    - cd nginx-1.6.2
    ```
  - 编译安装  
    ```diff
    - ./configure    
    - make && make install  
    ```
* nginx配置
  -复制/usr/local/nginx/conf/nginx.conf到/myapp/project/conf文件夹下，以下为修改部分
  
      user  root;
      worker_processes  1;

      error_log  /myapp/project/logs/error.log;
      error_log  /myapp/project/logs/error.log  notice;
      error_log  /myapp/project/logs/error.log  info;

      http {
          include       /usr/local/nginx/conf/mime.types;
          default_type  application/octet-stream;

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
      }
## 项目启动
  * 启动uwsgi和nginx
    - 进入/myapp/project/conf中运行uwsgi.ini
      ```diff
      - uwsgi --ini uwsgi.ini
      ```
    - 进入/myapp/project/conf中运行nginx.conf
      ```diff
      - nginx -c /myapp/project/conf/nginx.conf
      ```
  * 调试问题常用命令
    - 查看某个端口是否被占用
      ```diff
      - netstat  -anp  |grep   端口号
      ```
    - 杀死uwsgi和nginx进程
      ```diff
      - killall -9 uwsgi
      - killall -9 nginx
      ```
    - 重启nginx
      ```diff
      - nginx -c /myapp/project/conf/nginx.conf -s reload
      ```
    
    
    









  
