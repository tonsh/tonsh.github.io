---
layout: post
title: Mysql 同步备份方案
date:   2013-11-28 23:50:33
categories: Mysql backup
---

<pre class="reference">
这是一篇以前在自己博客写的旧文章，最近考虑要不要关闭以前的博客(好久没被更新过了 >_<)。 所以挑了一篇想留下的搬过来。
</pre>

应用程序上线后,不得不考虑的一个问题便是数据库备份及实现生成环境与开发环境的数据库同步。下面是我想到的一个解决方案：开发环境每天凌晨 3 点触发线上的 Mysql 备份脚本，然后将线上的备份文件拷贝到本地,并导入开发 Mysql 数据库。

线上的备份脚本 /home/youremote/shell/mysql\_make\_backup.sh

{% highlight bash %}
date=`date + %Y%m%d`
dir=/home/youremote/mysqlbk
file=${dir}/mysqlbk.${date}.sql.bz2
 
mysqldump --add-drop-table -uusername -ppassword databasename | bzip2 > ${file}
 
#删除7天前的备份文件
find ${dir} -mtime +7 -exec rm -f {} \;
{% endhighlight %}

开发环境下的定时脚本 /home/yourlocal/shell/mysql\_make\_import.sh

{% highlight bash %}
#!/bin/sh
 
date=`date + %Y%m%d`
filename=mysqlbk.${date}.sql.bz2
remoteFile=/home/youremote/mysqlbk/${filename}
localdir=/home/yourlocal/import
 
#运行线上的备份脚本
ssh tonsh@tonsh.net /home/youremote/shell/mysql_make_backup.sh
#将备份文件拷贝到本地
scp tonsh@tonsh.net:${remoteFile} ${localdir}
#更新本地 mysql
bunzip2 < ${localdir}/${filename} | mysql -uusername -ppassword databasename
{% endhighlight %}

加入定时任务，新建文件cronfile（或 crontab -e), 加入一下内容：

```
0 3 * * * /home/yourlocal/shell/mysql_make_import.sh
```

运行 crontab

```
crontab cronfile
crontab -l
    0 3 * * * /home/yourlocal/shell/mysql_make_import.sh
```

定时任务每天凌晨3点会触发开发环境的 shell 脚本 mysql\_make\_import.sh, 该脚本先触发远程服务器(生成环境) Mysql 备份脚本; 然后将远程服务器的备份文件通过 scp 保存至本地(开发环境); 最后将保存后的备份文件导入开发数据库。

这样既保证了生成环境 Mysql 数据库的备份，也能使开发环境数据同步。当然，如果开发环境没必要与生成环境数据同步，可省去数据导入的步骤。
