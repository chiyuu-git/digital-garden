---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2023-01-10-Tue, 8:06:24 pm","date-modified":"2023-04-29-Sat, 8:38:48 pm","permalink":"/programming/basic/common/vmware/","dgPassFrontmatter":true}
---


[VMware下载、安装、卸载、使用 - 知乎](https://zhuanlan.zhihu.com/p/272541376)

[Windows 10 22H2 - MSDN - 山己几子木](https://msdn.sjjzm.com/win10.html

[Vmware去虚拟化Win10虚拟机 - 『逆向资源区』 - 吾爱破解 - LCG - LSG |安卓破解|病毒分析|www.52pojie.cn](https://www.52pojie.cn/thread-1686086-1-1.html)

蓝屏的看过来！！！！！！！！！！！！

配置里的 cpuid 问题，换成其他型号的！！！

打开，Windows 10 x64.vmx

删除 cpuid.开头的

# 虚拟机去虚拟化

先装一个普通的 win, 然后再复制过去就可以了

如果虚拟机不能双开, 把枪炮分到另一个账号就很蠢了, 所以当务之急是研究双开策略

# Ip 代理

并没有简单好用的公网 ip 方案, 毕竟是公网 ip, 通过桥接模式, 不勾选复制主机状态, 可以实现网络访问, 但是此时是获取不到 ip 地址的, 感觉问题也不小呀

通过网络无法查询到自己的公网 ip, ipconfig 也只能看到一个局域网 ip 地址
