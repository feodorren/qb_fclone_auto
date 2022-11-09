# qb_fclone
use qbittorrent to download files, then fclone them to upload files, finally delete it once uploaded to GoogleDrive..

## 0. 参考和鸣谢

```https://blog.ba7lcy.com/windows-xia-shi-yong-sh-jiao-ben-yi-qbittorrent-rclone-zi-dong-shang-chuan-jiao-ben-wei-li/```
```https://hostloc.com/thread-639215-1-1.html```
```https://hostloc.com/thread-612238-1-1.html```
```https://p3terx.com/archives/offline-download-of-onedrive-gdrive.html```


## 1. 安装种子下载服务器qbittorrent

### 1.1 安装jq

debian & linux

  ```
  apt-get install -y jq
  ```

centos 下

  ```
  yum install -y jq
  ```


### 1.2 安装qbittorrent

https://github.com/userdocs/qbittorrent-nox-static/releases 选择一下适合自己主机的版本。
```
wget -qO /usr/local/bin/x86_64-qbittorrent-nox https://github.com/userdocs/qbittorrent-nox-static/releases/download/release-4.4.2_v2.0.6/x86_64-qbittorrent-nox
```

把二制文件放在bin目录下面，给二进制文件700权限
```
chmod 700 /usr/local/bin/x86_64-qbittorrent-nox
```
给qBittorrent编写进程守护文件，让其开机启动，也可以手动让程序停止或开始。

输入，然后回车，如果没有nano命令，用vi也可以。注意这里用了简写qbt的名字，原名字太长，影响输入：

```
nano /etc/systemd/system/qbt.service
```

然后添加如下内容：
```[Unit]
Description=qBittorrent Service
After=network.target nss-lookup.target

[Service]
UMask=000
ExecStart=/usr/local/bin/x86_64-qbittorrent-nox --profile=/usr/local/etc

[Install]

WantedBy=multi-user.target
```
Ctrl+O 保存，Enter保存文件名，Ctrl+X退出nano编辑器。

请注意：–profile=/usr/local/etc这个选项，表示qBittorrent的配置目录，如果不写，就是在当前用户的主目录，例如root目录下面。我建议按上面的选项填写，这样规范一些。

启动关闭命令：
```
systemctl enable qbt   #开机启动，第一次必须要执行此命令
systemctl start qbt  #启动
systemctl stop qbt  #停止
systemctl status qbt  #软件运行状态查询
```

到目前为止，qBittorrent就算安装成功了，打开浏览器，输入：IP:8080，就可以打开qBittorrent的网页界面了。

用户名：admin
密码：adminadmin

如果是PT玩家，可以自行关闭DHT。


## 2. 配置fclone

### 2.1 下载fclone文件
根据版本自行前往下载
```
https://github.com/mawaya/rclone
```
根据我的服务器amd64下载的版本为以下：
```
https://github.com/mawaya/rclone/releases/download/fclone-v0.4.1/fclone-v0.4.1-linux-amd64.zip
```
在本地解压后得到fclone文件。

### 2.2 配置fclone
上传fclone文件至服务器`/usr/bin`文件夹内

给fclone赋予执行权限
```
chmod +x /usr/bin/fclone
```

### 2.3 自行获取google drive api 下的ID  和 secret

https://rclone.org/drive/
 参考Making your own client_id章节

### 2.4 获取sa账号并保存以后使用

#### 2.4.1 创建sa账号，保存至以后使用

https://youtu.be/SkmulhqSXm0
#### 2.4.2 添加sa账号到群组

https://youtu.be/I1F_z1QKoGs

### 2.5 上传sa账号清单和sa账号json结尾文件至服务器`/home/sa_account/`下
例如 `/home/sa_account/sa_all.xlsx` 和 `/home/sa_account/sa/`

### 2.6 配置fclone
#### 2.6.1 输入
`fclone config`
进行配置
参考https://rclone.org/drive/ 的Configuration 章节

其中需要填写sa账号列表地址以及sa账号文件夹
例如 `/home/sa_account/sa_all.xlsx` 和 `/home/sa_account/sa/`

远程获取token

  下载rclone windows版本到本地

  打开cmd面板

  ```
    cd c:/Users/Administrator/Desktop/reclone文件夹
  ```   

  ```
    rclone authorize "drive" "xxxxxxxxxxxxxxxxxxx此处省略4行"
  ```

  生成token后复制到debian命令行界面 。

  至此feclone配置完毕。

## 3. 上传脚本，并配置自动运行。
在服务器上执行命令，下载脚本

```
wget https://raw.githubusercontent.com/feodorren/qb_fclone/main/qb_auto.sh
```
给脚本可执行权限
```
chmod +x /root/qb_auto.sh
```

修改qbittorrent设置

![image](https://user-images.githubusercontent.com/22808709/200754482-d33b97cc-75e4-4797-ab1b-6df54bd39fd2.png)

