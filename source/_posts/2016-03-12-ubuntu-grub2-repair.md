---
title:  "Ubuntu修复grub2引导"
date:   2016-03-12 20:43:15
categories: Linux
---
一般情况下，我们工作会使用Linux，而工作之外则会使用Windows。如果购买两台PC，不仅浪费，而且麻烦，总是在两台PC之间拷贝文件，双系统通常是我们的选择。

先安装Windows，再安装Linux一般是不会出现问题的。然而，如果先安装了Linux，再安装Windows，或者双系统电脑重装Windos系统时，就会出现Linux引导丢失的问题。这是因为安装Windows时会将MBR重写，不会再由grub2进行引导。如何解决

1.若有安装Ubuntu的liveCD

1.1进入liveCD的Ubuntu系统
<!-- more -->
1.2查看可用设备信息
{% codeblock lang:c %}
#lsblk
{% endcodeblock %}
出现一坨东西，找到自己的Linux分区（/与/boot），假设sda1为/boot；sda2/ubuntu-vg-root为/

1.3挂载Linux分区
{% codeblock lang:c %}
#mount /dev/mapper/ubuntu-vg-root /mnt/
#mount /dev/sda1 /mnt/boot/
{% endcodeblock %}

1.4共享/dev目录
{% codeblock lang:c %}
#mount -o bind /dev/ /mnt/dev/
{% endcodeblock %}

1.5改变参考根路径
{% codeblock lang:c %}
#chroot /mnt/
{% endcodeblock %}

1.6安装grub引导
{% codeblock lang:c %}
#grub-install /dev/sda
{% endcodeblock %}

如果出现一坨东西，如果最后出现Installation finished. No error reported.证明安装成功，重启就可以了。

[github][github]

[github]: https://github.com/jlqian


