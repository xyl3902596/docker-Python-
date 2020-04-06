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
    
