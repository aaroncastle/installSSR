# GCP server 安装SSR（免费）

<font size=5 weight=bold color=\#66ffff>这是一个可以流畅的在外网看4K视频或勉强看8k视频的一个教程，不用花一分钱。</font>



## 先决条件：

1，有个能临时科学上网的条件；

2，有张VISA信用卡（不花钱）

## 设置

- 设置防火墙；
- 设置静态IP；
- 开启一个实例，外网用静态IP；

## 创建虚拟机实例（服务器）

- 进入实例

- `sudo su`  [切换到管理员帐号它是 super user do switch user的缩写]

- `uname -r` 查看centos 内核 

- 如果是类似这样的 `3.10.0-514.2.2.el7.x86_64` 说明内核低于3.9，要开启google的BBR

- <font color=#6666FF>BBR是谷歌写的一个TCP协议下的传输拥堵的算法，我们访问页面是基于TCP协议的HTTP协议，可以加速传输速度。</font>

- <font color=#6666ff>如果你没有一点linux的基础，下面就别问了，跟我输入命令就行了</font>

- ### 升级内核

  - 进入Linux系统后，先切换到超级管理员命令是：`sudo su`
  - 查看内核是多少: `uname -f`
  - 如果内核大于4.9，下面不用看了，直接执行一下BBR加速的最后一步，去验证BBR是否开启，如果没有出现`bbr`字样，说明没有开启BBR加速，从`BBR加速`开始跟着步骤走。如果出现了`bbr`字样，直接安装SSR。Linux只熟悉CentOS,说明一下：CentOS 6,CentOS 7内核低于4.9，CentOS 8是高于4.9的并默认开启BBR的，但CentOS8无法按照下面教程安装SSR。CentOS6没测试，习惯CentOS6的按下面流程走就是。如果你能用“习惯”来形容对CentOS程度，下面教程也没必要看，作为运维出身的你开启BBR是小儿科，我用的CentOS版本和一线大厂的版本保持一致: <font color=#FF0033>CentOS 7.5 1804版本</font>。
  - `sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org`
  - ` sudo rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm`
  - `sudo yum --enablerepo=elrepo-kernel install kernel-ml -y`
  - `rpm -qa | grep kernel`
  - `egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'`
  - `grub2-set-default 0`
  - `reboot`
  - 再次查看内核
  - `uname -r`
  - 数值大于4.9就可以启动BBR加速了

- ### BBR加速（能看4K视频的关键，没开启BBR加速，可能加载图片都困难）

  - `echo 'net.core.default_qdisc=fq' | sudo tee -a /etc/sysctl.conf `
  - `echo 'net.ipv4.tcp_congestion_control=bbr' | sudo tee -a /etc/sysctl.conf`
  - `sudo sysctl -p`
  - 验证一下是否已经开启：
  - `sudo sysctl net.ipv4.tcp_available_congestion_control`
  - 如果是类似于下面的字符,就是已经开启了BBR，可以安装SSR了。其实也可以先安装SSR再开BBR加速：
  - <font color=red>bbr</font> 

- ### 安装SSR

  - 先看下有没有安装wget，输入命令`wget`,如果没有出现没有找到这个命令的提示，说明已经安装有wget,下面的一步不用做了。
  - 安装`yum install wget` 可以在后加上参数 `-y`。这样就不用中间做选择了。如果忘记了，没任何关系，安装时会有询问，这东西多大，安装吗？输入`y`回车确定就行。
  - `wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh`
  - `chmod +x shadowsocks-all.sh`
  - `./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log`
  - 选择版本、对称加密类型、传输协议、混合类型。等待一会儿就安装完成。
  - 把生成的ssr地址选中，就下去复制了。window电脑右键点客户端，会有从剪切板导入服务器的选项。
  - 把remark（备注）改成你想区分的名字，group（分组）分成你想归类的组就完成了。然后左键点客户端一下，切换成你的服务器就OK了。

