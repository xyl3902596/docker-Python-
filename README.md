# docker-入门
适用对象:
-----
    对docker一无所知的python算法工程师(我就是),通过本文的学习,你将初步认识什么是docker,以及如何利用docker将你的python项目打包起来,发布到docker hub, 并在同事本地下载你的docker镜像.
安装:
----
    https://www.runoob.com/docker/ubuntu-docker-install.html
docker是什么
----
    最通俗的语言来说,docker是在打包你的项目的同时,连环境一起打包的技术.docker里有两个非常重要的概念, images,container,简单来说,images是镜像,相当于面向对象里面的类,container是容器,相当于面向对象里面的实例,那么类是可以生产实例的,在dcoker里不同的是 容器也可以生成镜像.
    总体步骤:主机A build镜像---push到docker hub---主机B pull镜像
准备工作
----
    docker几乎所有指令都需要sudo权限,因此可以将用户名添加到docker用户组里,指令如下:
    groups  #查看现在用户所在组的组成员,这时候应该是没有docker的
    sudo groupadd docker # 添加docker用户组
    sudo gpasswd -a $USER docker	#USER处是你自己的用户名,用你的用户名代替,将你的用户名加到docker用户组里
    newgrp docker  #更新用户组
    groups #这时再查看用户所在组的组成员,发现这时候有docker了,但是很可能你再开一个shell groups还是没有docker,我弄了好久,电脑重启以后就有了.
Build镜像
----
    项目文件夹里应有两个文件:1.项目代码文件夹 2.Dockerfile
    Dockerfile里写的是项目打包成镜像的一些细节. 如:
     
    FROM python:2.7   #工作python环境
    ADD ./docker_file /file  #将本地docker_file里的所有文件放到镜像中file文件夹里面
    WORKDIR /file  #工作目录为/file
    RUN pip install -U pip  #以下3行为pip换阿里源
    RUN pip config set global.index-url http://mirrors.aliyun.com/pypi/simple
    RUN pip config set install.trusted-host mirrors.aliyun.com:
    RUN pip install -r requirements.txt #pip项目所需依赖包,可以通过pipreqs ./ 指令自动生成
    CMD ["python", "/file/evaluate_image.py"]  #项目执行文件
    
    写好Dockerfile以后,建立images文件
    docker build -t [image_name] ./

push镜像到docker hub
----
    先去https://hub.docker.com/,注册一个账号,比如我的账号是xyl123,然后建立一个仓库,如first,那么这个仓库就可以用xyl123/first来指代.
    docker login #登录docker
    docker tag images_name xyl123/first
    docker push xyl123/first
    这时打开自己的docker hub主页会发现已经存在了刚上传的images
pull镜像到本地
----
    这是docker很重要的一个指令,因为它不仅可以将同事做好的镜像pull到本地,也可以把别人的镜像pull到本地
    docker pull xyl123/first #通过运行docker images 你会发现本地多出了一个镜像,接下来你就可以利用images的相关指令对pull下来的images进行操作了.
    
images相关指令
-----
    docker images   #查看目前所有的images文件
    docker rmi [images_id]  #删除某个images
    docker tag images_name[:tag] [user_name]/[project_name_on_docker_hub]  #将images push到docker hub的必要操作
    docker run -it images_name /bin/bash  #运行镜像,产生一个容器,并以shell的交互形式运行容器.
container相关指令
----
    docker ps [-a] #查看正在运行的container,-a可以查看所有的container
    docker start container_id #运行某个container
    docker stop container_id #停止某个container
    docker exec -it container_id /bin/bash #进入某个container
    exit #退出container,退出后容器并不停止,这是和 docker attach container_id 方式进入容器的根本的不同
容器和本地运行结果的相互拷贝
----
    docker cp container_name:文件在container的地址 本地地址,拷贝container的文件到本地,举例如下:
    docker cp testtomcat：/usr/local/tomcat/webapps/test/js/test.js /opt
    docker cp 本地地址 container_name:文件在container的地址,拷贝本地文件到container,举例如下:
    docker cp /opt/test.js testtomcat：/usr/local/tomcat/webapps/test/js
容器生成images
----
    修改后的容器想要再次生成镜像
    docker commit docker_id new_images_name
