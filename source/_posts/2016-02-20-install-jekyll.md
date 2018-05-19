---
title:  "Ubuntu安装jekyll"
date:   2016-02-20 17:51:46
categories: jekyll
---
使用github pages希望在本地预览后，再提交到github上，所以希望能够在本地安装jekyll用来预览。

在Ubuntu系统上可以使用

{% codeblock lang:c %}
sudo apt-get install ruby2.0
{% endcodeblock %}
<!-- more -->
安装ruby2.0，但是在Ubuntu上会建立ruby1.9的软连接ruby，即

{% codeblock lang:c %}
ruby -v
{% endcodeblock %}

得到的是ruby 1.9.3p484 (2013-11-22 revision 43786) [x86_64-linux]

我们需要使用的是ruby2.0，可以修改软连接指向ruby2.0。同理，gem也需要进行相同修改。

接下来我们安装jekyll

{% codeblock lang:c %}
sudo gem install jekyll
{% endcodeblock %}

ERROR:  Could not find a valid gem ‘rails’ (>= 0), here is why: Unable to download data from https://rubygems.org/ – Errno::ETIMEDOUT: Connection timed out – connect(2) for “s3.amazonaws.com” port 443 (https://api.rubygems.org/latest_specs.4.8.gz) 

这是被墙的原因，我们可以修改源，使用国内的镜像。

{% codeblock lang:c %}
gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
{% endcodeblock %}

确保只有ruby.taobao.org源
{% codeblock lang:c %}
gem sources -l
{% endcodeblock %}

如果出现：

{% codeblock lang:c  %}
*** CURRENT SOURCES ***

https://ruby.taobao.org/
{% endcodeblock %}

说明已经成功，可以进行下一步

{% codeblock lang:c  %}
sudo gem install jekyll
{% endcodeblock %}

出现错误信息：
{% codeblock lang:c  %}
 ERROR:  Error installing rails: 
 ERROR: Failed to build gem native extension.

/usr/bin/ruby2.0 extconf.rb mkmf.rb can’t find header files for ruby at /usr/lib/ruby/include/ruby.h 
{% endcodeblock %}

这时因为没有安装ruby2.0-dev导致的，在ruby1.9时还有详细提示，ruby2.0没有提示……

{% codeblock lang:c  %}
sudo apt-get install ruby2.0-dev
{% endcodeblock %}

最后重新执行：

{% codeblock lang:c  %}
sudo gem install jekyll
{% endcodeblock %}

安装成功，可以到page目录进行启动：

{% codeblock lang:c  %}
jekyll server
{% endcodeblock %}

访问：http://127.0.0.1:4000/就可以了

[github][github]

[github]: https://github.com/jlqian


