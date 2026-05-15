路由器搭建openclash记录

## 过程
最开始用小米4c路由器尝试，搜了一下，说要通过一个漏洞打开telnet端口。
好不容易找到了这个https://github.com/acecilia/OpenWRTInvasion链接
执行了这里面的脚本，打开了telnet端口，但是依然不能用ssh和scp，ftp也不会用，
还有遇到了权限问题，最后问ai才用ftp成功上传openwrt固件，备份了eeprom文件，刷上了openwrt。
然后尝试安装openclash,openclash github页面提供了安装命令，我集成到了一个sh文件，通过scp传到了路由器，
执行脚本第二行报错：dnsmasq-full和luci-base依赖安装失败，发现需要先把原来的dnsmasq卸了再装，luci-base似乎是因为我在win上记事本处理的文本，留下了特殊符号导致安装失败。
执行脚本第三行报错：这一行要下载openclash.ipk文件，这时我的路由器还访问不了外网所以失败，删掉。
给安装包改了名，执行脚本，终于显示正在安装openclash，但是又报错，显示闪存剩余空间不够。
想了一下，尝试安装到内存，opkg安装命令加了一个参数。
这回安装了两分钟，然后重启了......
找了各种方法尝试避免重启（排查重启日志试图删掉重启命令），还是放弃了。
想用闲置荣耀路由器刷openwrt，发现比小米4c还难，几乎不可能。
然后还是花238买了个软路由
-238
到了之后为了放心尝试刷入openwrt版本固件，使用uboot界面刷入后变砖。
慌了，尝试刷sysupgrade固件好了。
然后发现包管理器是apk不是opkg，问了ai尝试转换安装包格式、装两个包管理器，最后还是重刷了用opkg的老版本。
配置了半天wifi中继，终于连上互联网。
按照之前经验成功安装上openclash，眼花缭乱的界面和配置，研究了半天终于成功连上外网了。

## 安装教程
1. 不管是什么路由器，先想办法刷openwrt。如果你是买的软路由，那应该可以按照厂商给的方法进入uboot界面，
打开doh.openwrt.org，输入你的路由器型号，找到下载链接，记住这个路径（后面用/划分的），打开downloads.openwrt.org,按照之前的路径在24.10.6版本下面找对应路由器的sysupgrade固件。
2. 在uboot界面上传，重装。（变砖了没事，只要uboot还在就能刷合适的固件补救）
3. 用一根网线连接电脑和路由器LAN口，如果电脑没有就用转接头
4. 打开路由器热点，如果需要wifi中继，建议luci-proto-relay+relayd
5. 在openclash的github界面下载最新的ipk文件，自行判断是nftable/iptables(问ai)，复制对应的安装脚本到一个sh文件（后缀名为sh），sh和ipk文件上传到路由器，通过ssh登陆到路由器输入opkg install bash，安装bash。
6. 输入vi [这里填sh文件路径]（vim编辑器自行搜索用法），把第二行末尾如果有特殊符号删掉，第三行删掉，把ipk文件改名为“openclash.ipk”，然后保存退出编辑器。
7. 输入bash [这里填sh文件路径]，回车，第一遍应该会显示dnsmasq-full安装失败，输入opkg remove dnsmasq，把之前的删掉，然后再输入bash [省略]。
8. 等待几分钟之后路由器重启，openclash应该安装好了。现在用浏览器输入路由器地址（一般是192.168.1.1）访问管理页面，在服务一栏下面点开openclash，运行模式增强、代理模式规则、区域绕过停用、域名嗅探停用、dns代理启用、流媒体解锁停用。
9. 搭建服务器参考https://bulianglin.com/archives/nicename.html
10. 点击配置订阅，点击添加，输入复制的节点链接，UDP支持启用，点击两次保存配置，再点击更新配置
11. 点击配置管理，左边的文件把rules的其他部分删掉，改成"MATCH,[你的代理名字]"，保存应用。
