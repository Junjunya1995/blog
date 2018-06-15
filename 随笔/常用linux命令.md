
    kill -9 \`lsof -t -i:port\`  杀死对应port的进程      
    
    #awk 统计数量 并排序
    awk '{print $1}' access.log |sort | uniq -c | sort -rn | head -10
