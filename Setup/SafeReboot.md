# Safe Reboot
由于我的Ubuntu电脑是一台笔记本，所以为了防止内存不够或者缓存有问题，设置了每周定时重启一次。  
具体是在crontab上进行设置，先在terminal上面打开crontab：  
```
sudo crontab -e
```
然后在最底下加入这行：

```
0 3 * * 1 /usr/local/bin/safe-reboot.sh
```
意思是在每周一3:00 A.M.运行这个脚本，这个脚本就是重启脚本，脚本内容如下：
```
#!/bin/bash

# ========= 用户配置 =========
LOAD_THRESHOLD=2.0
USER_NAME=$(whoami)
LOGFILE="/var/log/safe-reboot.log"
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")
TO_EMAIL="your_email@example.com"
MAIL_SUBJECT_OK="✅ Ubuntu 定时重启执行成功"
MAIL_SUBJECT_FAIL="⚠️ Ubuntu 定时重启跳过（有任务运行）"

# ========= 函数：邮件发送 =========
send_mail() {
    local subject="$1"
    local body="$2"
    echo -e "$body" | mail -s "$subject" "$TO_EMAIL"
}

# ========= 函数：GPU 是否空闲 =========
gpu_busy=false
if command -v nvidia-smi &> /dev/null; then
    gpu_procs=$(nvidia-smi | grep -E "python|train|conda" | wc -l)
    [[ "$gpu_procs" -gt 0 ]] && gpu_busy=true
fi

# ========= 函数：CPU 负载检查 =========
load=$(uptime | awk -F 'load average:' '{ print $2 }' | cut -d, -f1 | xargs)
load_compare=$(echo "$load > $LOAD_THRESHOLD" | bc)

# ========= 函数：是否有活跃用户任务 =========
running_tasks=$(pgrep -u "$USER_NAME" -fE "python|train|tmux|screen|jupyter|bash" | wc -l)

# ========= 判断是否满足重启条件 =========
if [[ "$load_compare" -eq 1 || "$running_tasks" -gt 0 || "$gpu_busy" == true ]]; then
    echo "$TIMESTAMP - ❌ 重启取消：CPU负载=$load, 活跃任务=$running_tasks, GPU在用=$gpu_busy" >> "$LOGFILE"
    send_mail "$MAIL_SUBJECT_FAIL" "取消自动重启：\nCPU 负载: $load\n运行中任务: $running_tasks\nGPU 是否占用: $gpu_busy"
else
    echo "$TIMESTAMP - ✅ 满足条件，执行重启。" >> "$LOGFILE"
    send_mail "$MAIL_SUBJECT_OK" "Ubuntu 已在 $TIMESTAMP 成功执行定时重启。\nCPU负载: $load\n任务数: $running_tasks\nGPU占用: $gpu_busy"
    /sbin/shutdown -r now
fi
```
当然这个脚本只是临时用ai生成，主要还是先完成每周定时重启，并留下了一定的后续拓展空间。
