
    kill -9 \`lsof -t -i:port\`  杀死对应port的进程      
    
    ----------------------------------------------------
    
    #awk 统计数量 并排序
    awk '{print $1}' access.log |sort | uniq -c | sort -rn | head -10
    
    #awk 统计响应时间长的语句
    awk -F "\"" '{print $10,$2}' api.teacher.fudaodashi.com-access.log | sort
    
    ---------------------------------------------------
    
    #centos 最小化安装的时候不带tab补全
    yum -y install bash-completion
    
    -----------------------------------------------------
    
    #firewall防火墙开启端口  --permanent  表示永久开启 
    firewall-cmd --zone=public --add-port=80/tcp --permanent
    firewall-cmd --reload   //刷新
     

    解压
	tar –xvf file.tar //解压 tar包
	tar -xzvf file.tar.gz //解压tar.gz
	tar -xjvf file.tar.bz2   //解压 tar.bz2tar –xZvf file.tar.Z //解压tar.Z
	
    //自锁
    */1 * * * * /usr/bin/flock  -xn /var/run/test.lock -c '/home/test.sh'
    
    
    //快速进入docker
     docker exec -it  `docker ps | grep xuexi-go | awk '{print $1}'` /bin/bash