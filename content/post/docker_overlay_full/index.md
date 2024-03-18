---
title : "docker overlay文件系统占用100%"
description : "docker overlay文件系统占用100%"
date : "2024-03-16"
aliases : ["折腾"]
author : "飞天"
toc : true
---



### overlay2文件分析

看到占用磁盘空间最大的目录是docker存储根目录下的overlay2目录

```
du -sh  /var/lib/docker/overlay2/*
```



再查看docker存储根目录下的overlay2目录下哪个目录占用空间最大

```
du -s  /var/lib/docker/overlay2/*|sort -rn
```



找出最大的overlay2子目录：ef129273a3f100feec4970bee90b0b83d9f1b170f6a76668eba9bdcfbf5f4ac8

找到子目录关联的容器



findContainer.sh

```
containerIDList=$(docker ps -aq --no-trunc)
echo -n "Enter the target DirectoryName to search and locate around all containers: "
read target
for containerID in $containerIDList
do
    b=$(docker inspect $containerID|grep MergedDir|grep $target|wc -l)
    containerName=$(docker inspect --format '{{.Name}}' $containerID|awk -F "/" '{print $2}')
    if [ $b -ge 1 ];
    then
        echo ""
        echo  "命中，正在遍历容器：$containerName，结果是命中."
        echo ""
    else
        echo  "未命中，正在遍历容器：$containerName，结果是未命中."
    fi
done
```

执行 sudo sh findContainer.sh，按提示输入：ef129273a3f100feec4970bee90b0b83d9f1b170f6a76668eba9bdcfbf5f4ac8

```
# 脚本会输出ef129273a3f100feec4970bee90b0b83d9f1b170f6a76668eba9bdcfbf5f4ac8关联的容器ID

```

进入容器，然后找到对应的大日志文件，清空日志：

```
sudo cat /dev/null > xxxx.log
# 或者
sudo sh -c 'cat /dev/null > xxxx.log'
```



### container日志分析

```
for i in $(docker ps -q ); do 
        echo "ContainerID: $i"; 
        ls -alh $(docker inspect $i | grep LogPath|awk -F "\"" '{print $4}');
done;
```

上面的这条命令会打印出每个正在运行的容器标准输出日志占用的磁盘空间，可以选择超过一定体积（比如 1G）的日志文件情况，情况的方式可以很粗暴：

```
echo "" > xxx-json.log
# 或者
cat /dev/null > xxxx-json.log
```

