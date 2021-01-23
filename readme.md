H3C Lite 轻量级 H3C 802.1x 校园网认证 Linux/OpenWRT 客户端

更新：2021-01-22 网络认证改为网页认证，已无需此项目，故存档。

## 特点

轻量精简，单文件，源代码 5K，编译后仅 10K。使用原生 RAW socket，无需安装 Python（YaH3C 与 OH3C）、libpcap（njit-client）等任何支持库，便于部署到路由器中。同时，代码便于阅读。

## 使用

    h3clite username password [interface]

本程序只负责 H3C 认证，认证成功后请运行 dhclient 或 udhcpc 手动获取 IP 地址。

## 编译安装

- 桌面环境：

    gcc h3clite.c -std=c99 -o h3clite

- OpenWRT：
    1. 下载所需版本与硬件的交叉编译工具链
        - OpenWRT 12.09，硬件 RG100A：<http://downloads.openwrt.org/attitude_adjustment/12.09/brcm63xx/generic/OpenWrt-Toolchain-brcm63xx-for-mips-gcc-4.6-linaro_uClibc-0.9.33.2.tar.bz2>
        - OpenWRT 14.07，硬件 TP-Link WR720n：<https://downloads.openwrt.org/barrier_breaker/14.07/ar71xx/generic/OpenWrt-Toolchain-ar71xx-for-mips_34kc-gcc-4.8-linaro_uClibc-0.9.33.2.tar.bz2> 
    2. 将解压后的 `bin/` 加入环境变量 $PATH
    3. `mips-openwrt-linux-gcc h3clite.c -std=c99 -o h3clite`
    4. 将 h3clite 直接复制到路由器中，添加可执行权限 `chmod +x h3clite`，测试认证是否成功

## 定制

本项目适用于 NUDT 2016 版协议。欢迎有技术基础的朋友 fork 项目以定制自己学校的版本。一些要点：

1. 大部分情况只需修改文件末尾的 `eap_handle_identity` 函数。对于不同版本的 iNode，
请用 wireshark/tcpdump 抓包得到新的数据替换 `eap_handle_identity` 中的 base64 字串。
这部分的算法可参考 njit-client。
2. 如代码所示，SHNU 的 MD5 Challenge 实际上只是个 XOR，需要 MD5 算法的可从 njit-client 中粘一个过来。
3. 使用多播方式触发 EAPOL 请将 `s_broadcast_addr` 修改为 `01-80-c2-00-00-03`。
4. 某些协议会使用其它的 Request type，如用编号 7 的 ALLOCATED 代替 MD5，需要修改 `authentication` 中间相应部分。

## 致谢
本项目 fork 自 gmsj0001/shnu-h3c，略作修改以配合作者环境使用。感谢原作者 @gmsj0001。
