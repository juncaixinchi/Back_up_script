=Auto-backup scripts for linux

#设置免密码登录

\#A为本地主机(即用于控制其他主机的机器) ;
\#B为远程主机(即被控制的机器Server), 假如ip为10.157.31.20,用户名为user;
\#A和B的系统都是Linux

\#在A上的命令:
ssh-keygen -t rsa #(连续三次回车,即在本地生成了公钥和私钥,不设置密码)
scp ~/.ssh/id_rsa.pub user@10.157.31.20:.ssh/id_rsa.pub #(user为你的用户名，需要输入密码)

\#在B上的命令:
touch ~/.ssh/authorized_keys #(如果已经存在这个文件, 跳过这条)
chmod 600 ~/.ssh/authorized_keys  #(# 注意：必须将~/.ssh/authorized_keys的权限改为600)
cat ~/.ssh/id_rsa.pub  >> ~/.ssh/authorized_keys #(将id_rsa.pub的内容追加到 authorized_keys 中, 注意不要用 > ，否则  清空原有的内容，使其他人无法使用原有的密钥登录)

\#回到A机器:
ssh user@10.157.31.20 #(不需要密码, 登录成功)

\#如果需要登录其他机器，添加id_rsa.pub内容到相应authorized_keys文件内即可

#备份程序：

\#将远程的NAMD文件夹备份到本地BACKUP_DIR内
mkdir ~/BACKUP_DIR
vi ~/bin/Script_backup.sh
\#输入以下内容，注意修改相应ip、用户名

\#!/bin/sh
rm -rf ~/BACKUP_DIR/BACKUP.1
cp -al ~/BACKUP_DIR/BACKUP.0 ~/BACKUP_120/BACKUP.1
rsync -ave ssh lixw@10.157.31.20:~/NAMD ~/BACKUP_DIR/BACKUP.0

\#然后修改运行权限：
chmod a+x ~/bin/Script_backup.sh
\#可直接运行 Script_backup.sh 进行备份，即可将远程的NAMD文件夹备份到本地BACKUP_DIR内


#设置自动运行

\#利用系统自带的crond程序自动运行备份命令
crontab -e
\#输入
3：50运行Script_backup.sh）
crond restart # need root
\#然后就可以实现每天定时备份了
