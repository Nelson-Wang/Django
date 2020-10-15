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
    
  
    
