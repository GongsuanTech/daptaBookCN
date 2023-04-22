# 常见问题


## 我应该使用哪个浏览器?

我们推荐使用 [Chrome](https://www.google.com/chrome/) v.108及以上版本。

## 我可以运行多大的计算任务?

试用版当前的最大用户计算资源限制如下:

* 包括驱动程序和组件副本在内的最多**6个组件**。 
* 每个组件限制**1 Giga byte (Gi)内存**。
* 每个组件只能使用**1 个CPU**。
* 每个上传文件限制**1 Mega byte (Mi)存储**。
* 每个组件最多可以使用**5个用户输入文件**（不包括API文件）。
* 每次{term}`运行`最长时间为**24 小时**。

当超出这些限制时，您可能会遇到组件资源错误消息。

## 谁能看到我的数据?

您的{term}`运行`数据保存在由我们的云服务提供商[腾讯云](https://cloud.tencent.com/)管理的共享云资源上。尽管您的数据受到密码控制，但我们建议您暂时不要上传任何敏感数据到dapta平台。
我们可能有需要访问您的数据来实现技术支持和系统维护的目的。

## 我可以在一个组件运行中使用哪些软件?

下表提供了目前可用软件API的概述。请[联系技术支持](./Support.md)，以将您自己的操作系统或商业软件工具添加到列表中。  

| API 名称                 | Github 文件夹            | 可用软件 |
| --------                 | ---------                | ---------                    |
| calculix-fea-comp        | [generic-python3-comp](https://github.com/daptablade/generic-python3-comp) | Python 3.8, Calculix GraphiX (cgx_2.20), Calculix CrunchiX (ccx_2.20)| 
| generic-python3-comp     | [generic-python3-comp](https://github.com/daptablade/generic-python3-comp) | Python 3.8 only|
| generic-python3-driver   | [generic-python3-comp](https://github.com/daptablade/generic-python3-comp) | Python 3.8 only|
| libreoffice-comp         | [generic-python3-comp](https://github.com/daptablade/generic-python3-comp) | Python 3.8, LibreOffice (Version: 7.0.4.2)|
| openvsp-comp             | [generic-python3-comp](https://github.com/daptablade/generic-python3-comp) | Python 3.8, OpenVSP (Version: 3.31.1-Linux)|
| rescale-comp             | [generic-python3-comp](https://github.com/daptablade/generic-python3-comp) | Python 3.8, Dapta Rescale API (latest version)|


## 我可以使用哪些Python软件包?

当前的软件包列表： 
[Python 3.8 软件包](Packages%20list%20for%20python%203.8.md)。

(reference-FAQ-troubleshooting)=
## 故障排除

### 这是组件错误吗?

有时，您会遇到与用户定义组件代码相关的故障和执行错误。在工作区视图中，带有警告三角形的组件表示组件错误。您可以在组件日志中查看错误消息和回溯信息。要解决组件错误，请修改受影响的组件输入文件并重新上传。然后重新启动{term}`运行`。

### 其它故障 — 我能做些什么?

刷新您的浏览器，看看是否可以解决问题。在刷新尝试之间等待几分钟，因为云处理可能需要一些时间才能完成。特别是当您第一次开始一次新的{term}`运行`或清除会话时，情况往往是这样。

您可以通过从`接口控制 > ... > 运行管理`菜单中选择`终止`选项来强制终止会话（停止运行并删除所有内容）。

### 请求帮助

请参见我们提供的[技术支持](./Support.md)选项。