
    kill -9 \`lsof -t -i:port\`  杀死对应port的进程      
    
    ----------------------------------------------------
    
    #awk 统计数量 并排序
    awk '{print $1}' access.log |sort | uniq -c | sort -rn | head -10
    
    ---------------------------------------------------
    
    #centos 最小化安装的时候不带tab补全
    yum -y install bash-completion
    
    -----------------------------------------------------
    
    #firewall防火墙开启端口  --permanent  表示永久开启 
    firewall-cmd --zone=public --add-port=80/tcp --permanent
    firewall-cmd --reload   //刷新
     