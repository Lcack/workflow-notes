# Safe Reboot
由于我的Ubuntu电脑是一台笔记本，所以为了防止内存不够或者缓存有问题，设置了每周定时重启一次。  
具体是在crontab上进行设置

```bash
0 3 * * 1 /usr/local/bin/safe-reboot.sh
```

