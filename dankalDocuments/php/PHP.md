关于centos 7+使用docker安装LNMP环境

系统Centos 7.4

	安装Docker ：yum -y install docker	
	启动Docker服务:/bin/systemctl start docker.service
	记得安装一下vim:  apt update--->yum install apt
	
### 安装Mysql:5.7  ### 
  		docker pull mysql:5.7  
		编译：
  		docker run -d -p 3307（主机端口）:3306（指向端口） -e MYSQL_ROOT_PASSWORD=json123456 --name（命名） json_mysql mysql:5.7（此处可以用PULL下来的镜像ID）

### PHP   ###
		拉取镜像：docker pull mysql:5.6   ||  docker pull php:7.0-fpm
		编译:docker run -d -v /var/nginx/www/html:/var/www/html -p 9000:9000 --link xy_mysql:mysql --name json_phpfpm php:7.0-fpm 
### Ngnix ###
		拉取镜像：docker pull nginx:1.10.3
		编译：docker run -d -p 80:80 --name json_nginx -v /var/nginx/www/html:/var/www/html--link json_phpfpm:phpfpm --name json_nginx nginx:1.10.3

#### 常用命令： ####慢慢补充啦
		docker exec -ti xxxx /bin/bash ：进入XX容器
		




