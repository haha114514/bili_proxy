# bili_proxy
一个整合clash规则，使用国内低带宽服务器来解锁b站番剧和直播姬的教程

## 前言

本次教程是采用国内各大云厂商的便宜低带宽大陆服务器来配合clash分流，为海外用户解锁b站番剧和直播姬推流（配合tun虚拟网卡）。

思路：因为这些服务器虽然便宜（参考腾讯云1c2g5m 3年只需要288，还可以配合代理8折），但是带宽很有限，一个人看视频直播都很吃力。

然而，经过抓包和研究，b站番剧和直播姬开播拉取直播码服务器等验证操作每次都只是经过某些特定的api域名，视频的缓冲和直播姬的推流方法都是没有常时的ip验证的。

所以理论上是可以只用代理这些有参与验证ip的api域名，然后直播推流和番剧缓冲都走直连，从而达到小带宽还可以多人共用来解锁b站番剧和直播姬直播的目的。

本次教程采用腾讯云上海免费升级的2C4G6M服务器举例，搭建shadowsocks代理，并编写clash配置文件，来代理b站网页和直播姬的验证ip地址。

请勿用国内服务器做任何违反中华人民共和国相关法律的行为。

## 参考配置


![image](https://user-images.githubusercontent.com/47912037/114272259-d0741480-9a58-11eb-837a-f5030301f8f5.png)
![image](https://user-images.githubusercontent.com/47912037/114272260-d23dd800-9a58-11eb-9809-29de68a7bd52.png)

## 注意事项

1.由于腾讯云 阿里云等国内主流云默认出口一般都为电信，到部分海外运营商（你的本地运营商）连接较差，虽然只代理了部分api域名，但是依然会出现打开b站网页打开慢/直播姬拉取信息慢等情况。

2.由于视频缓冲和直播推流的cdn服务器是根据这个国内ip的位置和运营商决定的，如果购买腾讯云的话，建议日本 北美 澳洲的用户选择腾讯云上海，香港 东南亚 等用户考虑选择腾讯云深圳。

3.不排除叔叔后面会新增某些新的ip验证机制导致本教程失效。如果出现这种情况，请窒息。

4.如果你是日本Softbank宽带用户，直接购买腾讯云上海即可，Softbank到电信和b站分配的各类电信出口cdn访问很不错。

4.1 如果你是港澳台 东南亚用户，个人比较推荐使用腾讯云上海1C2G5M的服务器（年费99，找代理70-80）配合Azure100(价格约为30-40，100USD的余额可以使用大约800GB流量）开一台免费的B1s香港用于中转。上海电信163到香港Azure约为31-32ms，丢包率很低，可以同时将腾讯云服务器用于架设低负载的游戏服务器，例如无mod的MC服务器或者饥荒联机版服务器等。

# 中转教程


## 0.（可选）安装BBR用于增加网络稳定性和提升速度。


wget -N "https://github.000060000.xyz/tcp.sh" && chmod +x tcp.sh && ./tcp.sh

一般来说，安装xanmod版内核(选择6)，然后使用bbr+fq_pie加速（选择12）即可。

## 1.	安装Shadowsocks

这里建议使用这个一键脚本，并且有国内镜像，方便国内机器安装。

wget --no-check-certificate -O gost.sh https://raw.githubusercontent.com/KANIKIG/Multi-EasyGost/master/gost.sh && chmod +x gost.sh && ./gost.sh

先输入1进行安装,然后输入./gost.sh回到脚本菜单，选择'7. 新增gost转发配置'，然后选择'[4] 一键安装ss/socks5代理'，选择'[1] shadowsocks',然后输入你想设置的密码，加密方式一般选择'[3] chacha20-ietf-poly1305',最后设置一个端口作为服务端口（一般设置为10000-50000左右的任意一个端口）。
![image](https://user-images.githubusercontent.com/47912037/120561523-08d81380-c448-11eb-8c33-b9ee349a3086.png)
![image](https://user-images.githubusercontent.com/47912037/120561532-0e355e00-c448-11eb-87b1-4b9773735831.png)

## 2.往clash配置文件中添加节点信息

下载文件夹中的temp.yaml，按照上一步你自己设置的密码，端口，加密方式来修改节点的配置。如果是按照上一步提供的步骤提供的范例，应该设置成如下图。（server后面填入你国内服务器的ip，port为你设置的端口，cipher则为你选择的加密方式，password为你设置的密码。注意，这里只有password的参数需要加上两边的引号）
![image](https://user-images.githubusercontent.com/47912037/120562330-881a1700-c449-11eb-9c40-1300c51a1588.png)

## 3.安装clash并导入编辑好的配置文件

下载并安装最新版的*   [clash for Windows](https://github.com/Fndroid/clash_for_windows_pkg/releases) 。

打开软件，点选General，依次安装TAP Device和Service Mode，并打开system proxy。![image](https://user-images.githubusercontent.com/47912037/120676642-9748a580-c4d9-11eb-899b-e8b1e0167437.png)
![image](https://user-images.githubusercontent.com/47912037/120676708-a9c2df00-c4d9-11eb-931b-e943ccb99405.png)

点选Profile，并通过import按钮导入刚才编辑好的yaml文件。![image](https://user-images.githubusercontent.com/47912037/120676876-d37c0600-c4d9-11eb-8a98-a108d85cd7ce.png)

导入完毕之后，点选proxies，点开BILIBILI规则，选择我们刚才添加的“大陆节点”。![image](https://user-images.githubusercontent.com/47912037/120677038-02927780-c4da-11eb-9521-e0cc57c21128.png)

测试是否可以观看哔哩哔哩大陆限定番剧。

打开直播姬测试是否可以正常开播以及推流。![image](https://user-images.githubusercontent.com/47912037/120677357-543b0200-c4da-11eb-8d5f-67e7e532293b.png)
点开Connections栏目，选择按照Upload Speed来排序，点击上下箭头两次，这时候应该会见到一个往某个ip的1935端口上传的流量记录，并且流量类型为UDP，代理模式为DIRECT（这个时候为正常的RTMP over QUIC直播模式）。

## 4.常见问题
Q1：无法解锁番剧

A1:检查节点配置以及是否打开system proxy。

Q2：番剧可以看，但是直播姬不行。

A2：检查TAP Device和Serivice Mode相关的驱动是否安装成功，并且检查系统网络设备里的cfw-tap虚拟网卡是否正常工作。![image](https://user-images.githubusercontent.com/47912037/120677819-d1667700-c4da-11eb-808d-bf0eca51ca02.png)

Q3：🐦














